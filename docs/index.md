---
hide:
  - toc
---

**Recursive Length Prefix (RLP)** is a tiny, header-only **C++23** library for building and decoding canonical Ethereum-style RLP — without dragging in half a blockchain node just to serialize a tuple. It keeps the wire format honest, the type system sharp, and the code readable.

* :material-package-variant-closed:{.icon} **Header-only and lightweight** — drop `rlp.hpp` into your project and go; no linker drama, no runtime baggage
* :material-ethereum:{.icon} **Ethereum-compatible at the wire level** — emits canonical RLP strings and lists exactly as expected by Ethereum tooling
* :material-shield-check:{.icon} **Canonical by construction** — rejects non-minimal encodings, redundant sign extension, and malformed length forms
* :material-numeric:{.icon} **Typed integer support** — encode and decode unsigned and signed integers with explicit canonical policy
* :material-toggle-switch:{.icon} **Boolean support** — compact, deterministic mapping for `true` / `false`
* :material-format-letter-case:{.icon} **Text support** — works with `std::string`, `std::string_view`, and string literals
* :material-code-braces:{.icon} **Tuple and aggregate friendly** — tuples map naturally to RLP lists, with projection hooks for user-defined types
* :material-test-tube:{.icon} **Designed for verification** — small surface area, doctest-friendly, and easy to reason about at the byte level
* :material-language-cpp:{.icon} **Modern C++23 implementation** — concepts, constexpr-oriented design, and clear overload boundaries

Whether you're encoding Ethereum payloads, building compact binary protocols, or just want a sane typed façade over raw RLP bytes — **RLP gives you the wire format without the usual suffering**.

![RLP Byte Layout](assets/rlp-wire-format.png)

## :material-flash:{.icon} Features at a Glance

* :material-feather:{.icon} **Minimal footprint** — one public header, small API surface, straightforward integration
* :material-shape-outline:{.icon} **Strong type classification** — separates integers, text, byte strings, tuples, and projected user types cleanly
* :material-vector-polyline:{.icon} **Exact length-prefix handling** — computes payload and header sizes explicitly instead of guessing and patching later
* :material-format-list-bulleted-type:{.icon} **Structured list encoding** — tuples and projected aggregates become canonical RLP lists
* :material-lock-check:{.icon} **Strict decode path** — malformed or non-canonical payloads are rejected instead of quietly “helping”
* :material-cog-outline:{.icon} **Customizable projection layer** — specialize `from_tuple_t<T>` to decode directly into domain types
* :material-scale-balance:{.icon} **Predictable semantics** — byte-level compatibility with Ethereum RLP, plus optional typed extensions layered on top
* :material-hammer-wrench:{.icon} **Built for systems code** — easy to embed in trading systems, protocol stacks, crypto tooling, or test harnesses

## :material-rocket-launch:{.icon} From Value to Wire in Seconds

Get a value onto the wire with just a couple of lines:

```cpp
#include <rlp.hpp>
#include <array>
#include <tuple>
#include <cstdint>

int main() {
    auto payload = std::tuple {
        std::uint64_t{42}, true, std::string_view{"dog"}};
    auto out = rlp::encode(payload);
}
```

The resulting byte stream is canonical RLP, ready for protocol use, hashing, persistence, or Ethereum-adjacent tooling.

## :material-database-arrow-right:{.icon} Decode Back Into Types

Round-trip from wire format into structured C++ values:

```cpp
#include <rlp.hpp>
#include <array>
#include <tuple>
#include <string>

int main() {
    std::array<rlp::byte_t, 7> in{0xc6, 0x2a, 0x01, 0x83, 'd', 'o', 'g'};

    auto result = rlp::decode<std::tuple<std::uint8_t, bool, std::string>>(in);
    if (!result.ok)
        throw std::runtime_error("invalid RLP");
}
```
You get a strict decoder, explicit success/failure, and no hand-written byte fiddling. Civilisation, more or less.

## :material-shape-plus:{.icon} Decode Directly Into Your Own Types

Project tuples into domain objects with a tiny specialization:

```cpp
struct point_t {
    std::uint32_t x;
    bool y;
};

namespace rlp {
    template <>
    struct from_tuple_t<point_t> {
        using tuple_t = std::tuple<std::uint32_t, bool>;

        static constexpr point_t from(const tuple_t& v) {
            return {std::get<0>(v), std::get<1>(v)};
        }
    };
}
```
Now your decode path can target `point_t` directly, while the wire format stays plain old RLP.
## Who It’s For

<div class="grid cards" markdown>

-   :material-ethereum:{.icon} **Protocol & Blockchain Engineers**

    ---
    - Encode canonical RLP without pulling in a full client stack.
    - Build test vectors for Ethereum-adjacent systems.
    - Add lightweight RLP support to wallets, tools, and protocol adapters.
    - Keep wire-level logic explicit and inspectable.
    - Build binary protocol bridges around Ethereum-style payloads.
    - Generate deterministic byte payloads for signing, hashing, or transport.
    - Validate payloads with strict minimal-form decoding.
    - Use strict decoding to catch malformed inputs early.

- :material-chip:{.icon} **Systems Programmers**

    ---
    - Use a compact binary format with simple length-prefix rules.
    - Serialize tuples and small domain objects without reflection magic.
    - Keep dependencies low and integration friction near zero.
    - Embed the codec inside infrastructure, gateways, or simulators.
    - Test byte-exact protocol behavior with minimal ceremony.
    - Inspect and fuzz encoding rules in a small, readable codebase.
    - Integrate quickly into research or production code without framework sprawl.

</div>

