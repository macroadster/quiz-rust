# Problem 072: Mutable Reference to Shared Reference Invalidation

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `stacked-borrows`, `aliasing`, `miri`, `undefined-behavior`

## Problem Statement

A developer creates a raw pointer from a mutable reference, then creates a shared reference, and then writes through the raw pointer.

```rust
fn main() {
    let mut x: i32 = 1;
    let raw = &mut x as *mut i32;
    let shared = &x;

    unsafe {
        *raw = 42;
    }

    println!("{}", shared);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `42`
- B) Prints `1`
- C) Compilation error — cannot create `&x` while a mutable raw pointer exists
- D) Undefined behavior — writing through `raw` invalidates `shared`
