# Problem 081: Macro Hygiene and Variable Capture

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `macro_rules`, `hygiene`, `variable-capture`

## Problem Statement

A developer writes a macro that attempts to reference a variable from the calling scope:

```rust
macro_rules! add_to_x {
    ($val:expr) => {
        x + $val
    };
}

fn main() {
    let x = 10;
    let result = add_to_x!(5);
    println!("{}", result);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `15`
- B) Compilation error — `x` is not found in the macro's scope due to hygiene
- C) Prints `5` — `x` is treated as zero-initialized
- D) Compilation error — macros cannot use the `+` operator
