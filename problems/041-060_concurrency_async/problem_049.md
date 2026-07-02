# Problem 049: Waker Cloning and Wake Semantics

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Waker`, `RawWaker`, `wake`, `wake_by_ref`

## Problem Statement

A developer implements a custom future that delegates to an inner future but stores the waker for later use:

```rust
use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll, Waker};

struct WakerStasher<F> {
    inner: F,
    stashed_waker: Option<Waker>,
}

impl<F: Future + Unpin> Future for WakerStasher<F> {
    type Output = F::Output;

    fn poll(mut self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        // Stash the current waker
        self.stashed_waker = Some(cx.waker().clone());

        // Poll the inner future
        Pin::new(&mut self.inner).poll(cx)
    }
}
```

A key question arises: is it safe to stash `cx.waker().clone()` and call `.wake()` on it in a later poll invocation where the executor may have provided a *different* waker?

## Question

What is correct about waker replacement between polls?

## Options

- A) This is always safe — wakers are reference-counted and calling `.wake()` on a stale waker is harmless
- B) This is UB — once a new waker is provided, the old one is invalidated
- C) Calling `.wake()` on a stale waker is safe (won't cause UB) but may wake the wrong task or cause spurious wakeups — futures should always use the most recent waker
- D) This fails to compile because `Waker` does not implement `Clone`
