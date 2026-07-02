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

## Answer

**Correct: B)**

Let's trace through the types:

1. `numbers` is `Vec<i32>` (integer literals default to `i32` with no other constraints).
2. `.iter()` produces `std::slice::Iter<'_, i32>`, which yields `&i32` references.
3. `.map(|&x| x * 2)` — the pattern `&x` destructures the `&i32`, so `x: i32`. The expression `x * 2` returns `i32`. The map yields `i32` values.
4. `.collect::<Vec<_>>()` collects into `Vec<i32>`. The `_` is inferred as `i32`.
5. `doubled.first()` returns `Option<&i32>` — `first()` on a `Vec<T>` returns `Option<&T>`.
6. `first` is `Option<&i32>`, so `Some(&2)` is printed.

The key subtlety is that `.first()` returns a *reference* to the element, not the element itself. So the output is `Some(&2)`, not `Some(2)`.

## Common LLM Mistakes

1. **Forgetting that `first()` returns `Option<&T>`, not `Option<T>`:** LLMs often claim the output is `Some(2)` instead of `Some(&2)`. The `first()` method borrows from the collection.
2. **Thinking `_` is not allowed in turbofish:** Partial type inference with `_` is perfectly valid in turbofish syntax. `Vec<_>` tells the compiler "I want a Vec, infer the element type."
3. **Confusing `iter()` with `into_iter()`:** `.iter()` yields references (`&i32`), but the `|&x|` pattern destructures them, so the map closure works with owned `i32` values.

## Key Concepts

- `Vec::first()` returns `Option<&T>`, not `Option<T>`
- `_` is valid in turbofish syntax for partial type inference
- `iter()` yields references; pattern destructuring in closure args can remove the reference
- `Debug` formatting of references to integers shows `&2` not `2` when inside containers
