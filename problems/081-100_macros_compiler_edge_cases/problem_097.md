# Problem 097: Const Panic and Compile-Time Assertion

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `const-eval`, `const-panic`, `compile-time-errors`, `assert`

## Problem Statement

A developer uses `panic!` in a `const` context to create a compile-time assertion:

```rust
const fn checked_divide(a: i32, b: i32) -> i32 {
    if b == 0 {
        panic!("division by zero")
    } else {
        a / b
    }
}

const A: i32 = checked_divide(10, 2);
const B: i32 = checked_divide(10, 0);

fn main() {
    println!("{}", A);
}
```

## Question

What happens when this program is compiled?

## Options

- A) Prints `5` — `B` is never used so its panic is not evaluated
- B) Compilation error — `panic!` is not allowed in `const fn`
- C) Compilation error — const evaluation of `B` panics with "division by zero"
- D) Runtime panic — "division by zero" when `B` is accessed
