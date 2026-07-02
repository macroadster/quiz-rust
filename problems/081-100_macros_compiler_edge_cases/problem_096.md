# Problem 096: Const Fn Limitations — Mutable References

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `const-fn`, `const-eval`, `mutable-references`, `compile-time`

## Problem Statement

A developer writes a `const fn` that uses mutable references internally:

```rust
const fn sum_array(arr: &[i32]) -> i32 {
    let mut total = 0;
    let mut i = 0;
    while i < arr.len() {
        total += arr[i];
        i += 1;
    }
    total
}

const TOTAL: i32 = sum_array(&[1, 2, 3, 4, 5]);

fn main() {
    println!("{}", TOTAL);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `15`
- B) Compilation error — `const fn` cannot use `while` loops
- C) Compilation error — `const fn` cannot use mutable local variables
- D) Compilation error — `const fn` cannot index slices with `arr[i]`

## Answer

**Correct: A)**

This program compiles and prints `15`. Modern Rust's const evaluation is quite powerful:

- **Mutable local variables** are allowed in `const fn` (stabilized in Rust 1.46)
- **`while` loops** are allowed in `const fn` (stabilized in Rust 1.46)
- **Slice indexing** (`arr[i]`) works in `const fn` — this is essentially a bounds-checked pointer offset
- **`+=` and other compound assignments** work on primitive types in const context

The const evaluator (CTFE — Compile-Time Function Evaluation) runs a MIR interpreter that can handle loops, branches, and mutable locals. The key restrictions that remain are:
- No heap allocation (`Box`, `Vec`, etc.)
- No trait objects or dynamic dispatch
- No floating-point operations in some contexts
- No calls to non-const functions

## Common LLM Mistakes

1. **Thinking `const fn` cannot use `while` loops:** This was true before Rust 1.46, but loops in const fn have been stable since then. LLMs trained on older documentation often claim this is an error.
2. **Thinking `const fn` cannot use mutable locals:** Mutable local variables in const fn were stabilized alongside loops in Rust 1.46. The mutability is purely local and doesn't violate const evaluation rules.
3. **Confusing const fn restrictions with older Rust versions:** The restrictions on const fn have been progressively relaxed. LLMs sometimes apply restrictions from Rust 1.30-era const fn, which were much more limited.

## Key Concepts

- `const fn` supports mutable local variables and `while` loops (since Rust 1.46)
- Slice indexing works in const context
- The CTFE engine interprets MIR and can handle complex control flow
- Key remaining restrictions: no heap allocation, no dynamic dispatch, no non-const function calls
