# Problem 068: repr(transparent) and Newtype FFI

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `repr-transparent`, `newtype`, `ffi`, `layout`

## Problem Statement

A developer creates newtype wrappers and tries to pass them to an `extern "C"` function.

```rust
use std::mem;

#[repr(transparent)]
struct Meters(f64);

struct Seconds(f64);

extern "C" {
    fn some_c_function(distance: f64) -> f64;
}

fn main() {
    println!("Meters size: {}", mem::size_of::<Meters>());
    println!("Seconds size: {}", mem::size_of::<Seconds>());

    let m = Meters(100.0);
    let s = Seconds(9.58);

    // Which of these is guaranteed safe for FFI?
    // let _ = unsafe { some_c_function(m.0) };           // Line A
    // let _ = unsafe { some_c_function(mem::transmute(m)) }; // Line B
    // let _ = unsafe { some_c_function(mem::transmute(s)) }; // Line C
}
```

## Question

Which of the uncommented calls would be valid if uncommented? (Assume `some_c_function` is correctly linked.)

## Options

- A) Only Line A is valid
- B) Lines A and B are valid; Line C is undefined behavior
- C) All three lines are valid — both structs have the same layout as `f64`
- D) Lines A and B are valid; Line C is a compilation error

## Answer

**Correct: B)**

- **Line A** is always valid: extracting the inner `f64` with `.0` and passing it directly is fine.
- **Line B** is valid because `Meters` has `#[repr(transparent)]`, which **guarantees** it has the same ABI and layout as its single non-zero-sized field (`f64`). Transmuting `Meters` to `f64` is defined behavior.
- **Line C** is undefined behavior: `Seconds` uses the **default** Rust representation (`repr(Rust)`). While it currently happens to have the same size as `f64` (and both print `8`), the Rust language does **not** guarantee that a default-repr single-field struct has the same ABI as its field. The compiler is free to pass it differently in function calls.

This is exactly why `repr(transparent)` exists — to provide the guarantee that Line C cannot provide.

## Common LLM Mistakes

1. **Claiming all three are valid because both structs are 8 bytes:** Same size does not mean same ABI. `repr(Rust)` makes no guarantees about ABI compatibility, even for single-field structs. `repr(transparent)` exists precisely for this guarantee.
2. **Claiming Line C is a compile error:** It compiles fine — `Seconds` and `f64` are the same size, so `transmute` accepts it. The problem is a semantic/ABI one, not a syntactic one.
3. **Claiming Line B is UB:** `repr(transparent)` explicitly guarantees layout and ABI compatibility with the inner type, making this transmute well-defined.

## Key Concepts

- `#[repr(transparent)]` guarantees identical layout **and ABI** to the single non-ZST field
- Default `repr(Rust)` structs have **no** layout or ABI guarantees, even with one field
- `transmute` checks sizes at compile time but cannot verify ABI compatibility
- `repr(transparent)` is essential for safe FFI with newtype wrappers
