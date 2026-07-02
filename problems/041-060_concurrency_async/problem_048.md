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
