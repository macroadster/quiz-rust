# Problem 070: Shared Reference to Mutable Data via UnsafeCell

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `UnsafeCell`, `interior-mutability`, `aliasing`, `stacked-borrows`

## Problem Statement

A developer uses `UnsafeCell` for interior mutability and creates aliasing pointers.

```rust
use std::cell::UnsafeCell;

fn main() {
    let cell = UnsafeCell::new(42i32);

    let r1: &i32 = unsafe { &*cell.get() };
    let r2: *mut i32 = cell.get();

    unsafe {
        *r2 = 100;
    }

    println!("{}", r1);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `100`
- B) Undefined behavior — writing through `r2` while `r1` (a shared reference) is live
- C) Prints `42`
- D) Compilation error — cannot create `&i32` from `UnsafeCell`
