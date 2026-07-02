# Problem 078: PhantomData and Variance

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `PhantomData`, `variance`, `lifetimes`, `type-system`

## Problem Statement

A developer creates wrapper types using `PhantomData` with different configurations and tests lifetime variance.

```rust
use std::marker::PhantomData;

struct Covariant<'a> {
    _marker: PhantomData<&'a ()>,
}

struct Invariant<'a> {
    _marker: PhantomData<&'a mut ()>,
}

fn use_covariant<'a>(_c: Covariant<'a>) {}
fn use_invariant<'a>(_c: Invariant<'a>) {}

fn main() {
    let long_lived = String::from("hello");

    {
        let cov = Covariant { _marker: PhantomData };
        let inv = Invariant { _marker: PhantomData };

        // Both created with an inferred short lifetime
        use_covariant(cov); // Line A
        use_invariant(inv); // Line B
    }

    // Does this compile?
    let cov_outer: Covariant<'static> = Covariant { _marker: PhantomData };
    let inv_outer: Invariant<'static> = Invariant { _marker: PhantomData };

    fn accepts_short<'a>(c: Covariant<'a>) {}
    fn accepts_short_inv<'a>(i: Invariant<'a>) {}

    accepts_short(cov_outer);       // Line C
    accepts_short_inv(inv_outer);   // Line D
}
```

## Question

Which lines, if any, cause a compilation error?

## Options

- A) No errors — all lines compile
- B) Line D fails — `Invariant<'static>` cannot be passed where `Invariant<'a>` (shorter) is expected
- C) Lines C and D both fail — `'static` cannot be shortened to `'a`
- D) Line B fails — `Invariant` cannot have its lifetime inferred
