# Problem 094: ref vs & in Patterns — Subtle Difference

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `pattern-matching`, `ref-keyword`, `binding-modes`, `references`

## Problem Statement

A developer writes two apparently equivalent match expressions:

```rust
fn main() {
    let value = &42i32;

    // Version A: using & in pattern
    let a = match value {
        &x => x,
    };

    // Version B: using ref in pattern
    let b = match *value {
        ref x => x,
    };

    println!("a: {} ({})", a, std::any::type_name_of_val(&a));
    println!("b: {} ({})", b, std::any::type_name_of_val(&b));
}
```

## Question

What is the output of this program?

## Options

- A) `a: 42 (i32)` and `b: 42 (&i32)`
- B) `a: 42 (&i32)` and `b: 42 (i32)`
- C) `a: 42 (i32)` and `b: 42 (i32)`
- D) Compilation error — cannot use `&x` pattern on `&i32`
