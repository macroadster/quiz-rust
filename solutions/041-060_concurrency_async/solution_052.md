# Problem 052: Async Trait Methods and Object Safety — Solution

## Answer

**Correct: B)**

As of Rust 1.75+, `async fn` in traits works with static dispatch (`impl Trait`) but **not** with dynamic dispatch (`dyn Trait`). The trait is not object-safe.

The reason: `async fn call(&self, input: &str) -> String` desugars to something like `fn call(&self, input: &str) -> impl Future<Output = String> + '_`. Each implementor of the trait returns a **different concrete future type**. A vtable (used for `dyn Trait`) needs to know the exact return type, but `impl Future` is opaque and varies per implementation.

This is the same reason `fn foo(&self) -> impl SomeTrait` makes a trait non-object-safe. The return type is not a single concrete type — it varies by implementor.

To use async traits with dynamic dispatch, you need to erase the future type:
- Use the `#[trait_variant::make(ServiceDyn: Send)]` pattern
- Use `async-trait` crate (which boxes the future)
- Manually return `Box<dyn Future<Output = String> + Send + '_>`

## Common LLM Mistakes

1. **Choosing A** — LLMs trained on post-1.75 information sometimes overstate what RPITIT enables. It supports static dispatch but *not* `dyn` dispatch for async traits.
2. **Choosing D** — Adding `Send` to the future doesn't help with object safety. The fundamental issue is that `dyn` requires a single concrete return type, not that the future isn't `Send`.
3. **Conflating RPITIT with full trait object support** — RPITIT solves the "async fn in trait" problem for generics, not for trait objects. These are distinct problems in Rust's type system.

## Key Concepts

- `async fn` in traits (RPITIT) works with `impl Trait` (static dispatch) since Rust 1.75
- `dyn Trait` requires object safety — `-> impl Future` violates this
- The `async-trait` crate boxes the future to enable dynamic dispatch
- Object safety requires that return types are concrete and identical for all implementors
