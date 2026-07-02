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
