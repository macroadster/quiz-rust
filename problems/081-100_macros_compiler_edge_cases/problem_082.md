# Problem 082: Push-Down Accumulation in Recursive Macros

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `macro_rules`, `push-down-accumulation`, `tt-munching`, `recursive-macros`

## Problem Statement

A developer uses the push-down accumulation pattern to reverse a list of tokens at compile time:

```rust
macro_rules! reverse {
    ([] $($reversed:tt)*) => {
        stringify!($($reversed)*)
    };
    ([$first:tt $($rest:tt)*] $($reversed:tt)*) => {
        reverse!([$($rest)*] $first $($reversed)*)
    };
}

fn main() {
    let s = reverse!([a b c]);
    println!("{}", s);
}
```

## Question

What is the output of this program?

## Options

- A) `"a b c"`
- B) `"c b a"`
- C) `"c  b  a"` (with extra spaces)
- D) Compilation error — macro recursion limit exceeded
