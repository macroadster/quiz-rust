# Problem 063: Null Pointer Dereference vs Null Pointer Creation

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `raw-pointers`, `null-pointers`, `undefined-behavior`

## Problem Statement

A developer explores the distinction between creating and dereferencing null pointers.

```rust
fn main() {
    let p: *const i32 = std::ptr::null();
    let q: *const i32 = 0 as *const i32;

    println!("p is null: {}", p.is_null());
    println!("p == q: {}", p == q);
    println!("size: {}", std::mem::size_of_val(&p));
}
```

## Question

What is the output of this program?

## Options

- A) Compilation error — cannot create null raw pointers
- B) `p is null: true` / `p == q: true` / `size: 8` (on 64-bit)
- C) Undefined behavior — creating a null pointer is UB
- D) `p is null: true` / `p == q: false` / `size: 8` (on 64-bit)
