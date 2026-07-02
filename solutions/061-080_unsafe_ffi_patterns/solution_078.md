# Problem 078: PhantomData and Variance — Solution

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
