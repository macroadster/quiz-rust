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

## Answer

**Correct: B)**

Each closure in Rust has a unique, anonymous type — even if two closures have identical signatures, they are different types. The `if`/`else` expression requires both branches to produce the same type, but `|x| x + x` and `|x| x + 1` are two distinct closure types.

The `impl Fn(i32) -> i32` return type means "some single concrete type that implements `Fn(i32) -> i32`." It does not mean "any type that implements this trait" — that would be `dyn Fn(i32) -> i32`. Since the compiler cannot determine a single concrete type for both branches, this fails to compile.

To fix this, you would need to use `Box<dyn Fn(i32) -> i32>` and box both closures, or restructure the code to return the same closure type from both branches.

## Common LLM Mistakes

1. **Thinking `impl Trait` allows different concrete types:** LLMs often confuse `impl Trait` (opaque type, must be a single concrete type) with `dyn Trait` (dynamic dispatch, allows different types). `impl Trait` in return position means exactly one concrete type.
2. **Assuming closures with the same signature have the same type:** This is perhaps the most common misunderstanding. Each closure expression generates a unique anonymous type, even with identical bodies.
3. **Thinking the compiler can "unify" the closure types:** Unlike integer literals, closure types cannot be unified. They are always distinct types.

## Key Concepts

- Each closure in Rust has a unique, anonymous type
- `impl Trait` in return position requires a single concrete type across all return paths
- `dyn Trait` (with `Box`) is needed for returning different concrete types
- Closures with the same `Fn` signature are not the same type
