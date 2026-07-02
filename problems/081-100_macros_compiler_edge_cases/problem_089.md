# Problem 089: Type Inference with Turbofish and Collect

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `type-inference`, `turbofish`, `collect`, `iterators`

## Problem Statement

A developer uses `collect()` with partial type annotations:

```rust
fn main() {
    let numbers = vec![1, 2, 3, 4, 5];

    let doubled = numbers
        .iter()
        .map(|&x| x * 2)
        .collect::<Vec<_>>();

    let first: Option<_> = doubled.first();
    
    println!("{:?}", first);
    println!("{}", std::any::type_name_of_val(&doubled));
}
```

## Question

What is the output of this program?

## Options

- A) `Some(2)` and `alloc::vec::Vec<i32>`
- B) `Some(&2)` and `alloc::vec::Vec<i32>`
- C) `Some(2)` and `alloc::vec::Vec<&i32>`
- D) Compilation error — `_` cannot be used with turbofish in `collect::<Vec<_>>()`
