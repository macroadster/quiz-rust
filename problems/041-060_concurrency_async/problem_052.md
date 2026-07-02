# Problem 052: Async Trait Methods and Object Safety

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `async-trait`, `dyn`, `object-safety`, `RPITIT`

## Problem Statement

Starting from Rust 1.75, `async fn` is allowed in traits via Return Position Impl Trait in Traits (RPITIT). Consider:

```rust
trait Service {
    async fn call(&self, input: &str) -> String;
}

struct Echo;

impl Service for Echo {
    async fn call(&self, input: &str) -> String {
        input.to_string()
    }
}

fn use_static(service: &impl Service) {
    // works fine
}

fn use_dynamic(service: &dyn Service) {
    // does this work?
}
```

## Question

Does the function `use_dynamic` compile (using `&dyn Service`)?

## Options

- A) Yes, `dyn Service` works because `async fn` in traits is fully supported since Rust 1.75
- B) No, the trait is not object-safe because `async fn` returns an opaque `impl Future` type, and `dyn Trait` requires known return types
- C) No, but only because `call` takes `&self` — it would work with `self: Box<Self>`
- D) Yes, but only if you add a `Send` bound to the returned future
