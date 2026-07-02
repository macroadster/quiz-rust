# Problem 088: Closure Type Inference Across Branches

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `closures`, `type-inference`, `unification`, `higher-ranked-types`

## Problem Statement

A developer writes a function that returns a closure constructed in different branches:

```rust
fn make_adder(use_double: bool) -> impl Fn(i32) -> i32 {
    if use_double {
        |x| x + x
    } else {
        |x| x + 1
    }
}

fn main() {
    let f = make_adder(true);
    println!("{}", f(5));
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `10`
- B) Compilation error — the two closures have different types, and `if`/`else` branches must have the same type
- C) Prints `6`
- D) Compilation error — `impl Fn` cannot be used as a return type for closures
