
[![CI](https://github.com/vargalabs/rlp/actions/workflows/ci.yml/badge.svg)](https://github.com/vargalabs/rlp/actions/workflows/ci.yml)
[![codecov](https://codecov.io/gh/vargalabs/rlp/branch/main/graph/badge.svg)](https://codecov.io/gh/vargalabs/rlp)
[![MIT License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19322459.svg)](https://doi.org/10.5281/zenodo.19322459)
[![GitHub release](https://img.shields.io/github/v/release/vargalabs/rlp.svg)](https://github.com/vargalabs/rlp/releases)
[![Documentation](https://img.shields.io/badge/docs-stable-blue)](https://vargalabs.github.io/rlp)

## Design Principles

RLP is built for environments where **correctness** is not a feature — it is a **constraint**. Every transformation is **deterministic**. Every **byte** has a reason to exist. There are no **hidden allocations**, no **implicit conversions**, and no **undefined behavior** hiding behind convenience. The library favors **structure** over heuristics:

- types are mapped **explicitly**, not guessed  
- encoding is **canonical**, not context-dependent  
- failures are **surfaced**, not deferred  

Performance follows from this: $\text{throughput} \approx \frac{\text{useful work}}{\text{branching} + \text{ambiguity} + \text{copies}}$ So the design removes **branching**, **ambiguity**, and unnecessary **copies**. This is not a framework. It is a small, **predictable** layer between **typed data** and **bytes** — intended for systems where **reproducibility**, **auditability**, and **latency** matter more than abstraction. If something looks simple, it is because the complexity was **removed**, not hidden.

## Build Matrix

| OS / Compiler | GCC 13      | GCC 14      | GCC 15      | Clang 17      | Clang 18      | Clang 19      |Clang 20       |
|---------------|-------------|-------------|-------------|---------------|---------------|---------------|---------------|
| Ubuntu 22.04  |![gcc13][200]|![gcc14][201]|![gcc15][202]|![clang17][250]|![clang18][251]|![clang19][252]|![clang20][253]|
| Ubuntu 24.04  |![gcc13][300]|![gcc14][301]|![gcc15][302]|![clang17][350]|![clang18][351]|![clang19][352]|![clang20][353]|

## Development

```bash
cmake -DCMAKE_BUILD_TYPE=Debug -DRLP_BUILD_TESTS=ON -S . -B build # configure (debug + tests)
cmake --build build --parallel                                    # build everything
cd build && ctest --output-on-failure                             # run tests
cmake --build build --target docs_build                           # install mkdocs locally
cmake --build build --target docs_serve                           # run webserver @ http://127.0.0.1:9000/
```

## Installation
```bash
sudo apt install build-essential cmake
cmake -DCMAKE_BUILD_TYPE=Release -S . -B build
cmake --build build --parallel
sudo cmake --install build
```

## Usage
After installation, RLP is available as a standard CMake package:

```cmake
find_package(rlp REQUIRED)
add_executable(app main.cpp)
target_link_libraries(app PRIVATE rlp::rlp)
```

[200]: https://vargalabs.github.io/rlp/badges/ubuntu-22.04-gcc-13.svg
[201]: https://vargalabs.github.io/rlp/badges/ubuntu-22.04-gcc-14.svg
[202]: https://vargalabs.github.io/rlp/badges/ubuntu-22.04-gcc-15.svg
[300]: https://vargalabs.github.io/rlp/badges/ubuntu-24.04-gcc-13.svg
[301]: https://vargalabs.github.io/rlp/badges/ubuntu-24.04-gcc-14.svg
[302]: https://vargalabs.github.io/rlp/badges/ubuntu-24.04-gcc-15.svg
[250]: https://vargalabs.github.io/rlp/badges/ubuntu-22.04-clang-17.svg
[251]: https://vargalabs.github.io/rlp/badges/ubuntu-22.04-clang-18.svg
[252]: https://vargalabs.github.io/rlp/badges/ubuntu-22.04-clang-19.svg
[253]: https://vargalabs.github.io/rlp/badges/ubuntu-22.04-clang-20.svg
[350]: https://vargalabs.github.io/rlp/badges/ubuntu-24.04-clang-17.svg
[351]: https://vargalabs.github.io/rlp/badges/ubuntu-24.04-clang-18.svg
[352]: https://vargalabs.github.io/rlp/badges/ubuntu-24.04-clang-19.svg
[353]: https://vargalabs.github.io/rlp/badges/ubuntu-24.04-clang-20.svg
[400]: https://vargalabs.github.io/rlp/badges/macos-13-gcc-13.svg
[401]: https://vargalabs.github.io/rlp/badges/macos-13-gcc-14.svg
[402]: https://vargalabs.github.io/rlp/badges/macos-13-gcc-15.svg
[450]: https://vargalabs.github.io/rlp/badges/macos-13-clang-17.svg
[451]: https://vargalabs.github.io/rlp/badges/macos-13-clang-18.svg
[452]: https://vargalabs.github.io/rlp/badges/macos-13-clang-19.svg
[453]: https://vargalabs.github.io/rlp/badges/macos-13-clang-20.svg