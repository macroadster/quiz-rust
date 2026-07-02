# Problem 085: Derive Macro Trait Bound Propagation

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `derive-macros`, `trait-bounds`, `generic-structs`, `Clone`

## Problem Statement

A developer derives `Clone` on a generic struct and uses it with a type that implements `Clone`, but through a `PhantomData` indirection:

```rust
use std::marker::PhantomData;

#[derive(Clone)]
struct Wrapper<T> {
    id: u64,
    _marker: PhantomData<T>,
}

struct NotClone;

fn main() {
    let w: Wrapper<NotClone> = Wrapper {
        id: 1,
        _marker: PhantomData,
    };
    let w2 = w.clone();
    println!("{}", w2.id);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `1`
- B) Compilation error — `NotClone` does not implement `Clone`, so `Wrapper<NotClone>` can't derive `Clone`
- C) Compilation error — `PhantomData<T>` requires `T: Clone` to implement `Clone`
- D) Prints `1` but only because `PhantomData` is zero-sized — the clone is a no-op

## Answer

**Correct: B)**

The `#[derive(Clone)]` macro generates an impl with trait bounds on *all* type parameters, regardless of whether they're actually needed structurally. The generated code looks roughly like:

```rust
impl<T: Clone> Clone for Wrapper<T> {
    fn clone(&self) -> Self {
        Wrapper {
            id: self.id.clone(),
            _marker: self._marker.clone(),
        }
    }
}
```

Note the `T: Clone` bound. Even though `PhantomData<T>` implements `Clone` unconditionally (regardless of whether `T: Clone`), the derive macro mechanically adds `T: Clone` because `T` is a type parameter of the struct. Since `NotClone` does not implement `Clone`, the bound `NotClone: Clone` is not satisfied, and calling `.clone()` fails.

This is a well-known limitation of derive macros — they add overly conservative bounds. A manual impl could omit the `T: Clone` bound and work correctly.

## Common LLM Mistakes

1. **Assuming derive macros are smart about PhantomData:** LLMs often reason that since `PhantomData<T>` implements `Clone` regardless of `T`, the derived `Clone` should work. But the derive macro doesn't analyze individual field types — it adds bounds for all type parameters.
2. **Thinking PhantomData means the bound is unnecessary:** While a *manual* impl could omit the bound, `#[derive(Clone)]` mechanically adds `T: Clone` for every type parameter `T`.
3. **Claiming it compiles and prints `1`:** This is the most common error — not understanding that derive macro bound generation is mechanical and conservative.

## Key Concepts

- `#[derive(Clone)]` adds `T: Clone` bounds for *all* type parameters, even if not structurally needed
- `PhantomData<T>` implements `Clone` unconditionally, but derive macros don't account for this
- This is a known limitation — manual impls can provide more precise bounds
- The same issue affects `#[derive(Debug)]`, `#[derive(PartialEq)]`, etc.
