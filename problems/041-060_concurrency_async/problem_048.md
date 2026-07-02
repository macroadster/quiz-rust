# Problem 048: Polling a Future After Completion

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Future`, `Poll`, `fuse`, `poll-after-ready`

## Problem Statement

What does the `Future` trait contract say about polling a future after it has returned `Poll::Ready`?

```rust
use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll};

struct OneShotFuture {
    completed: bool,
}

impl Future for OneShotFuture {
    type Output = i32;

    fn poll(mut self: Pin<&mut Self>, _cx: &mut Context<'_>) -> Poll<Self::Output> {
        if !self.completed {
            self.completed = true;
            Poll::Ready(42)
        } else {
            panic!("polled after completion!");
        }
    }
}

struct DoublePoller;

impl Future for DoublePoller {
    type Output = i32;

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        let mut inner = OneShotFuture { completed: false };
        let mut pinned = Pin::new(&mut inner);

        // Poll once — gets Ready(42)
        let _ = pinned.as_mut().poll(cx);
        // Poll again — what happens?
        pinned.as_mut().poll(cx)
    }
}
```

## Question

If `DoublePoller` is awaited, what happens on the second poll of `inner`?

## Options

- A) Undefined behavior — polling after `Ready` is UB
- B) The panic fires — polling after completion is not UB but the `OneShotFuture` chose to panic, and this is its right
- C) Compilation error — the compiler prevents polling a future after it returned `Ready`
- D) Returns `Poll::Ready(42)` again — futures are required to be idempotent

## Answer

**Correct: B)**

The `Future` trait documentation states that polling a future after it has returned `Poll::Ready` is **not undefined behavior**, but the behavior is *unspecified* and implementation-defined. A future may:
- Panic (as `OneShotFuture` does here)
- Return `Pending` forever
- Return `Ready` with a different value
- Return `Ready` with the same value

The `OneShotFuture` implementation explicitly panics on re-poll, which is a common and valid defensive pattern. The `FusedFuture` trait and `.fuse()` combinator exist specifically to provide a safe wrapper that returns `Pending` on re-polls.

Critically, this is **not** UB — it's simply a logic error. The compiler does not prevent it because `Pin<&mut F>` can be polled any number of times; the "don't poll after Ready" is a soft contract, not enforced by types.

## Common LLM Mistakes

1. **Choosing A** — LLMs sometimes confuse "unspecified behavior" with "undefined behavior." Polling after `Ready` is not UB in Rust — it doesn't violate memory safety. It may panic, hang, or produce garbage, but it won't cause unsoundness.
2. **Choosing C** — The type system does not prevent re-polling. `Pin<&mut Self>` is reborrowed on each `as_mut()` call, and there's nothing stopping you from calling `poll` again.
3. **Choosing D** — Futures are not required to be idempotent. The trait documentation explicitly says behavior after `Ready` is up to the implementation.

## Key Concepts

- Polling a future after `Ready` is not UB, but behavior is unspecified
- `FusedFuture` trait: provides `is_terminated()` to check if re-polling is safe
- `.fuse()` wraps a future to return `Pending` after completion
- Defensive implementations should panic on re-poll to catch bugs early
