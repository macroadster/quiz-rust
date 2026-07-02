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

## Answer

**Correct: C)**

With `#[repr(C)]`, fields are laid out in **declaration order** with C-compatible alignment and padding rules.

**Struct A** (`u8, u32, u8`):
- `x: u8` at offset 0 (1 byte) → 3 bytes padding to align `y`
- `y: u32` at offset 4 (4 bytes)
- `z: u8` at offset 8 (1 byte) → 3 bytes trailing padding (struct alignment = 4)
- Total: **12 bytes**

**Struct B** (`u32, u8, u8`):
- `y: u32` at offset 0 (4 bytes)
- `x: u8` at offset 4 (1 byte)
- `z: u8` at offset 5 (1 byte) → 2 bytes trailing padding (struct alignment = 4)
- Total: **8 bytes**

`repr(C)` guarantees field order matches declaration order, unlike the default Rust representation where the compiler may reorder fields to minimize padding. This is why `A` and `B` have different sizes despite having the same field types.

## Common LLM Mistakes

1. **Claiming both are 6 bytes (no padding):** LLMs sometimes forget that `repr(C)` still requires alignment. `u32` must be at a 4-byte-aligned offset, and the struct's overall size must be a multiple of its largest alignment.
2. **Claiming both are 12 bytes:** This would mean `B` also has maximum padding, but `B`'s layout packs the two `u8` fields after the `u32`, needing only 2 bytes of trailing padding.
3. **Claiming the compiler reorders fields:** With `repr(C)`, the compiler is **not** allowed to reorder fields. Field order is guaranteed to match declaration order. Only the default `repr(Rust)` allows reordering.

## Key Concepts

- `repr(C)` preserves field declaration order — no compiler reordering
- Padding is inserted between fields to satisfy alignment requirements
- Trailing padding ensures the struct's total size is a multiple of its alignment
- Field order significantly impacts struct size under `repr(C)`
