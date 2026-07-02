# Problem 051: Async Closures and Lifetime Capture — Solution

## Answer

**Correct: B)**

This code fails because of a fundamental lifetime issue with async closures. When the closure `|s| async move { s.len() }` is called with `&s` (a reference to the local `String`), the `async move` block captures the reference `s: &str`. The returned future borrows `s`, but the type parameter `Fut` in the function signature has no lifetime connection to the input `&str`.

The bound `F: FnOnce(&str) -> Fut` desugars to `F: for<'a> FnOnce(&'a str) -> Fut`, but `Fut` is a single concrete type — it cannot vary with the lifetime `'a`. The future returned by the closure *borrows* from the `'a` in the input, but `Fut` has no way to express this.

This is a well-known limitation. The workaround patterns include:
1. Using a helper trait: `trait AsyncFn<'a> { type Fut: Future<Output = usize> + 'a; fn call(self, s: &'a str) -> Self::Fut; }`
2. Boxing: `F: FnOnce(&str) -> Pin<Box<dyn Future<Output = usize> + '_>>`
3. Using the nightly `async Fn` trait syntax

## Common LLM Mistakes

1. **Choosing A** — LLMs often don't track the lifetime mismatch between the closure's input reference and the returned future. They see `async move` and assume ownership solves everything, but `async move` only moves `s: &str` — which is a *copy* of the reference, not ownership of the data.
2. **Choosing C** — While HRTBs are involved, the error isn't about *needing* HRTBs — the desugaring already introduces `for<'a>`. The problem is that `Fut` can't depend on `'a`.
3. **Choosing D** — `'static` isn't the issue here. The problem is that `Fut` is a single type that can't capture the varying lifetime from the function parameter.

## Key Concepts

- Async closures that borrow from their arguments produce futures whose type depends on the input lifetime
- `FnOnce(&str) -> Fut` desugars with `for<'a>` but `Fut` is a single type, not lifetime-parameterized
- This is a known GAT/async closure limitation in Rust
- Workarounds: trait with associated type, `Box<dyn Future + '_>`, or nightly async closures
