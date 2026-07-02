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
