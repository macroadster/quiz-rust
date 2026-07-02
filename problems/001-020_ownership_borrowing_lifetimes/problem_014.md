# Problem 014: Invariance of &mut T in Lifetimes

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `variance`, `invariance`, `mutable-references`, `lifetimes`

## Problem Statement

Consider this code that attempts to use `&mut` references with different lifetimes:

```rust
fn extend_ref<'a>(slice: &mut &'a str, s: &'a str) {
    *slice = s;
}

fn main() {
    let mut r: &str = "initial";
    {
        let s = String::from("temporary");
        extend_ref(&mut r, &s);
    }
    println!("{}", r);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `temporary`
- B) Compilation error: `s` does not live long enough — `r` outlives the inner block
- C) Compiles and prints `initial` because the mutation is scoped to the inner block
- D) Compilation error: cannot borrow `r` as mutable because `&mut &str` is not a valid type

## Answer

**Correct: B)**

The code does not compile. The function signature `fn extend_ref<'a>(slice: &mut &'a str, s: &'a str)` requires both `*slice` (the inner reference) and `s` to have the same lifetime `'a`. In `main`, `r` is `&str` with a long lifetime (it initially points to a `'static` string literal). When we call `extend_ref(&mut r, &s)`, the compiler must unify the lifetime of `r`'s inner reference with the lifetime of `&s`.

Here's where invariance matters: `&mut T` is **invariant** in `T`. Since `slice` is `&mut &'a str`, the `&'a str` inside cannot be coerced to a different lifetime — it must be exactly `'a`. If `&mut T` were covariant, the compiler could shorten `r`'s lifetime to match `s`'s shorter lifetime. But invariance prevents this shortening, so `'a` must simultaneously satisfy: (1) `r`'s reference is `&'a str` and (2) `s` is `&'a str`. Since `s` lives only in the inner block, `'a` must be short enough for `s`, but `r` is used after the inner block, requiring `'a` to be long. This contradiction causes the error.

This invariance is essential for soundness: if `&mut &'a str` were covariant, we could write a short-lived reference into `r` through the `&mut`, creating a dangling reference.

## Common LLM Mistakes

1. **Ignoring invariance of `&mut T`:** LLMs often apply covariance rules uniformly, thinking `&mut &'a str` can shorten `'a` like `&&'a str` can. But `&mut T` is invariant in `T`, so no lifetime coercion occurs through the `&mut`.
2. **Claiming the function signature is invalid:** `&mut &'a str` is a perfectly valid type — it's a mutable reference to an immutable string reference.
3. **Thinking the mutation is "scoped":** The mutation through `&mut r` modifies `r` itself (the outer variable), not a local copy. If this compiled, `r` would point to `s`'s data after `s` is dropped.

## Key Concepts

- `&mut T` is invariant in `T` — the `T` cannot be coerced to a subtype or supertype
- Invariance of `&mut T` prevents writing a shorter-lived reference through a mutable reference to a longer-lived one
- This invariance is necessary for soundness: without it, dangling references could be created
