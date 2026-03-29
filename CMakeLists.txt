# This file is part of the RLP project and is licensed under the MIT License.
#
# Copyright © 2025–2026 Varga Labs, Toronto, ON, Canada 🇨🇦
# Contact: info@vargaconsulting.ca

cmake_minimum_required(VERSION 3.22)
project(rlp VERSION 0.1.0 DESCRIPTION "Header-only Recursive Length Prefix (RLP) library"  LANGUAGES CXX)
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)
include(CTest)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)
    message(WARNING "In-source builds are discouraged. Skipping compile_commands.json symlink.")
else()
    execute_process(
        COMMAND ${CMAKE_COMMAND} -E create_symlink ${CMAKE_BINARY_DIR}/compile_commands.json ${CMAKE_SOURCE_DIR}/compile_commands.json
        RESULT_VARIABLE symlink_result OUTPUT_QUIET ERROR_QUIET)
endif()

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
option(RLP_BUILD_TESTS "Build tests" ${BUILD_TESTING})
set(git_commit_hash "unknown")
set(git_tag "")

find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${CMAKE_SOURCE_DIR}/.git")
    execute_process(
        COMMAND ${GIT_EXECUTABLE} rev-parse --short HEAD WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        OUTPUT_VARIABLE git_commit_hash OUTPUT_STRIP_TRAILING_WHITESPACE ERROR_QUIET)
    execute_process(
        COMMAND ${GIT_EXECUTABLE} describe --tags --abbrev=0 WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        OUTPUT_VARIABLE git_tag OUTPUT_STRIP_TRAILING_WHITESPACE ERROR_QUIET )
endif()

if(git_tag)
    set(rlp_version_string "${git_tag}")
else()
    set(rlp_version_string "${PROJECT_VERSION}")
endif()

add_library(rlp INTERFACE)
add_library(rlp::rlp ALIAS rlp)
target_compile_features(rlp INTERFACE cxx_std_23)
target_include_directories(rlp INTERFACE $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include> $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)
target_compile_definitions(rlp INTERFACE RLP_SOFTWARE_VERSION="${rlp_version_string}" RLP_SOFTWARE_COMMIT_HASH="${git_commit_hash}")

install( FILES ${CMAKE_SOURCE_DIR}/include/rlp.hpp DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install( TARGETS rlp EXPORT rlp_targets )
install( EXPORT rlp_targets FILE rlp_targets.cmake NAMESPACE rlp:: DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/rlp )
configure_package_config_file(
    ${CMAKE_SOURCE_DIR}/cmake/rlp_config.cmake.in ${CMAKE_BINARY_DIR}/rlp_config.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/rlp )

write_basic_package_version_file( ${CMAKE_BINARY_DIR}/rlp_config_version.cmake VERSION ${PROJECT_VERSION} COMPATIBILITY SameMajorVersion )
install( FILES ${CMAKE_BINARY_DIR}/rlp_config.cmake ${CMAKE_BINARY_DIR}/rlp_config_version.cmake DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/rlp )

if(RLP_BUILD_TESTS)
    enable_testing()
    add_subdirectory(thirdparty EXCLUDE_FROM_ALL)
    function(add_test_case file_name)
        add_executable(test_${file_name} test/${file_name}.cpp)
        set_target_properties(test_${file_name} PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR} )
        target_link_libraries(test_${file_name} PRIVATE rlp::rlp libdoctest )
        if(CMAKE_BUILD_TYPE MATCHES "Debug")
            if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
                target_compile_options(test_${file_name} PRIVATE -fsanitize=address,undefined -fno-omit-frame-pointer )
                target_link_options(test_${file_name} PRIVATE -fsanitize=address,undefined )
                target_compile_definitions(test_${file_name} PRIVATE DEBUG)
            endif()
        endif()
        add_test(NAME test_${file_name} COMMAND test_${file_name})
    endfunction()
    ## add test cases here ie `example.cpp`, the output is prefixed with `test-`
    add_test_case(example)
endif()

