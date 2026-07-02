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
