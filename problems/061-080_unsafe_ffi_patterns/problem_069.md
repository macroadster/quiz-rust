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
