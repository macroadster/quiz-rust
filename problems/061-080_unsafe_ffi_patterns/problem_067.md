# Problem 067: repr(C) Struct Field Ordering and Padding

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `repr-C`, `struct-layout`, `padding`, `alignment`

## Problem Statement

A developer compares the sizes of two structs with identical fields but different layouts.

```rust
use std::mem;

#[repr(C)]
struct A {
    x: u8,
    y: u32,
    z: u8,
}

#[repr(C)]
struct B {
    y: u32,
    x: u8,
    z: u8,
}

fn main() {
    println!("A: {}, B: {}", mem::size_of::<A>(), mem::size_of::<B>());
}
```

## Question

What does this program print?

## Options

- A) `A: 6, B: 6`
- B) `A: 8, B: 8`
- C) `A: 12, B: 8`
- D) `A: 12, B: 12`
