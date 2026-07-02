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

## Answer

**Correct: A)**

All lines compile. Here's the key insight about variance:

- **Covariant** (`PhantomData<&'a ()>`): If `'long: 'short`, then `Covariant<'long>` can be used where `Covariant<'short>` is expected. So `Covariant<'static>` can be passed to `accepts_short` — `'static` is a subtype of any `'a`. ✓

- **Invariant** (`PhantomData<&'a mut ()>`): The type is invariant *in general*, meaning you can't convert between `Invariant<'a>` and `Invariant<'b>` when `'a ≠ 'b`. **However**, when calling `accepts_short_inv(inv_outer)`, the compiler infers `'a = 'static` — it doesn't need to *shorten* the lifetime; it unifies `'a` with `'static`. The function is generic over `'a`, and `'static` is a valid choice for `'a`.

The critical distinction: invariance prevents **subtyping** (e.g., you can't pass `Invariant<'static>` to a function that specifically requires `Invariant<'short>` for some concrete shorter lifetime). But a generic function `fn f<'a>(x: Invariant<'a>)` simply picks `'a = 'static`, which works fine.

## Common LLM Mistakes

1. **Claiming Line D fails due to invariance:** This is the most common mistake. Invariance prevents subtyping coercions, but it does NOT prevent a generic function from being instantiated with `'static`. The compiler just picks `'a = 'static`.
2. **Confusing invariance with "can't use 'static":** Invariance means you can't convert between different lifetimes, not that any particular lifetime is forbidden. `Invariant<'static>` is a perfectly valid type.
3. **Over-applying variance rules:** Variance matters when the compiler needs to coerce one concrete lifetime to another. With generics, the compiler infers — it doesn't coerce.

## Key Concepts

- `PhantomData<&'a ()>` makes a type covariant in `'a`
- `PhantomData<&'a mut ()>` makes a type invariant in `'a`
- Invariance prevents lifetime **subtyping coercions**, not generic **instantiation**
- A generic function `fn f<'a>(x: Invariant<'a>)` can be called with any concrete lifetime
