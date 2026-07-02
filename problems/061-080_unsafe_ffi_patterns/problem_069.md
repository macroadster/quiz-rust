# Problem 069: repr(packed) and Reference Safety

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `repr-packed`, `alignment`, `references`, `undefined-behavior`

## Problem Statement

A developer creates a packed struct and tries to take references to its fields.

```rust
#[repr(packed)]
struct Packed {
    a: u8,
    b: u32,
    c: u8,
}

fn main() {
    let p = Packed { a: 1, b: 2, c: 3 };

    let ra = &p.a;
    let rb = &p.b;

    println!("{}, {}", ra, rb);
}
```

## Question

What happens when this program is compiled?

## Options

- A) Compiles and prints `1, 2`
- B) Undefined behavior — `rb` is an unaligned reference
- C) Compiles but panics at runtime due to unaligned access
- D) Compilation error — cannot take a reference to a field of a `repr(packed)` struct that may be unaligned

## Answer

**Correct: D)**

Since Rust 1.62 (stabilized from a deny-by-default lint earlier), taking a reference to a potentially unaligned field of a `#[repr(packed)]` struct is a **hard compilation error**. In `Packed`, field `b: u32` is at offset 1 (right after `a: u8`), which is not 4-byte aligned. Since references in Rust must always be properly aligned, `&p.b` would create an unaligned reference, which is UB. The compiler prevents this:

```
error[E0793]: reference to packed field is unaligned
```

Note that `&p.a` would be fine on its own because `u8` has alignment 1, so it's always aligned. But the compiler still rejects `&p.b` because `u32` requires 4-byte alignment.

The safe alternative is to use `addr_of!(p.b)` to get a raw pointer, then read with `ptr::read_unaligned`.

## Common LLM Mistakes

1. **Claiming it compiles and is UB:** This *was* the situation in older Rust versions (before the error was made a hard error). LLMs trained on older information may not know this is now a compile error.
2. **Claiming `&p.a` is also an error:** `u8` has alignment 1, so a reference to it is always properly aligned even in a packed struct. Only fields whose alignment is greater than 1 are problematic.
3. **Suggesting it's a runtime panic (option C):** Unaligned access is UB, not a defined panic. But it doesn't get that far — the compiler catches it.

## Key Concepts

- `#[repr(packed)]` removes padding, causing fields to be potentially unaligned
- References (`&T`) must always be properly aligned — this is a safety invariant
- Taking a reference to a potentially unaligned packed field is a **compilation error**
- Use `std::ptr::addr_of!` / `addr_of_mut!` and `ptr::read_unaligned` for packed fields
