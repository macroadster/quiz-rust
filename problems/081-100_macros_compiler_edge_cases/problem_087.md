# Problem 087: Integer Literal Type Fallback

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `type-inference`, `integer-fallback`, `i32`, `type-ambiguity`

## Problem Statement

A developer writes code that relies on integer literal type inference with a generic function:

```rust
fn print_type_of<T>(_: &T) {
    println!("{}", std::any::type_name::<T>());
}

fn main() {
    let x = 42;
    let y = x + 0u8;
    print_type_of(&y);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `u8`
- B) Prints `i32`
- C) Compilation error — cannot add `i32` and `u8`
- D) Compilation error — ambiguous integer type for `x`
