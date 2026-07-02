# Problem 046: Pinning a Future to the Stack — Solution

## Answer

**Correct: B)**

The future returned by an `async fn` does **not** implement `Unpin`. The compiler generates a state machine for `async fn compute()`, and this anonymous future type is `!Unpin` by default. `Pin::new()` has the signature `fn new(pointer: P) -> Pin<P> where P::Target: Unpin`, so it cannot be called with `&mut impl !Unpin`.

To pin this future to the stack, you would need either:
1. `unsafe { Pin::new_unchecked(&mut fut) }` — requires ensuring `fut` won't be moved afterward
2. Using `pin!()` macro from `std::pin` (stabilized in Rust 1.68): `let mut pinned = std::pin::pin!(compute());`
3. Boxing: `let mut pinned = Box::pin(compute());`

Note: Option C is also partially true — `fut` does need to be `mut` — but the `Unpin` bound error on `Pin::new()` is the primary and more fundamental error the compiler reports.

## Common LLM Mistakes

1. **Choosing A** — LLMs sometimes forget that async futures are `!Unpin` and assume `Pin::new()` works universally. The `Unpin` bound on `Pin::new()` is specifically designed to prevent this.
2. **Choosing C** — While `fut` does need `mut` to take `&mut fut`, the `Pin::new()` `Unpin` bound error fires first/independently and is the fundamental issue.
3. **Not knowing async futures are !Unpin** — This is a critical fact: the compiler-generated future types from `async fn` and `async {}` blocks are `!Unpin` because they may contain self-references across `.await` points.

## Key Concepts

- Futures from `async fn`/`async {}` are `!Unpin` by default
- `Pin::new()` requires `T: Unpin` — use `Pin::new_unchecked()` or `Box::pin()` for `!Unpin` types
- The `pin!()` macro provides safe stack-pinning for `!Unpin` types
- Self-referential state machines (async futures) are the primary reason `Pin` exists
