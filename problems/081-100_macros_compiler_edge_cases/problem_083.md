# Problem 083: macro_rules! Matching Ambiguity and Fragment Specifiers

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `macro_rules`, `fragment-specifiers`, `tt-munching`, `ambiguity`

## Problem Statement

A developer writes a macro with multiple arms that use different fragment specifiers:

```rust
macro_rules! what_am_i {
    ($e:expr) => { 1 };
    ($i:ident) => { 2 };
    ($l:literal) => { 3 };
}

fn main() {
    let a = what_am_i!(42);
    let b = what_am_i!(x);
    let c = what_am_i!(1 + 2);
    println!("{} {} {}", a, b, c);
}
```

## Question

What is the output of this program?

## Options

- A) `1 2 1`
- B) `3 2 1`
- C) `1 1 1`
- D) Compilation error — ambiguous macro arms
