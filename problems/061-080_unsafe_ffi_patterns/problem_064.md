# Problem 064: transmute Between Different-Sized Types

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `transmute`, `type-layout`, `compile-error`

## Problem Statement

A developer attempts to use `transmute` to convert between types.

```rust
use std::mem::transmute;

fn main() {
    let x: u32 = 42;

    let a: [u8; 4] = unsafe { transmute(x) };
    println!("a: {:?}", a);

    let b: u64 = unsafe { transmute(x) };
    println!("b: {}", b);
}
```

## Question

What happens when this program is compiled?

## Options

- A) Compiles and prints the byte representation of `42` and then `42`
- B) Compilation error on the second `transmute` — source and destination types have different sizes
- C) Compiles but the second `transmute` produces undefined behavior at runtime
- D) Compilation error on the first `transmute` — cannot transmute scalar to array

## Answer

**Correct: B)**

`std::mem::transmute` requires that the source and destination types have **exactly the same size**. The first transmute (`u32` → `[u8; 4]`) is fine because both are 4 bytes. The second transmute (`u32` → `u64`) fails at **compile time** because `u32` is 4 bytes and `u64` is 8 bytes. The compiler emits:

```
error[E0512]: cannot transmute between types of different sizes, or
              dependently-sized types
```

This is a compile-time check, not a runtime one — `transmute` is one of the few unsafe operations where the compiler enforces a size invariant statically.

## Common LLM Mistakes

1. **Claiming it compiles with runtime UB:** LLMs sometimes think transmute's safety guarantees are only checked at runtime. In fact, the size equality check is performed at compile time — `transmute` between differently-sized types is a hard compilation error.
2. **Claiming the first transmute is invalid:** `u32` and `[u8; 4]` are both 4 bytes, so transmuting between them is valid (and the resulting bytes represent the native-endian encoding of `42`).
3. **Confusing `transmute` with `transmute_copy`:** `transmute_copy` does allow reading a smaller type as a larger type (unsafely), but `transmute` does not.

## Key Concepts

- `transmute` enforces source and destination size equality at **compile time**
- `transmute` between same-sized types compiles but may still be UB (e.g., invalid bit patterns)
- `u32` → `[u8; 4]` is a valid and commonly used transmute
- For different-sized conversions, use `transmute_copy` (with extreme care) or explicit casts
