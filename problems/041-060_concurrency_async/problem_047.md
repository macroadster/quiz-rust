# Problem 047: Hand-Rolling a Future — Poll Semantics

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Future`, `Poll`, `Waker`, `hand-rolled`

## Problem Statement

A developer implements a custom `Future` that counts how many times it's polled:

```rust
use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll};

struct CountDown {
    remaining: u32,
}

impl Future for CountDown {
    type Output = &'static str;

    fn poll(mut self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        if self.remaining == 0 {
            Poll::Ready("done")
        } else {
            self.remaining -= 1;
            Poll::Pending
        }
    }
}

fn main() {
    let rt = tokio::runtime::Runtime::new().unwrap();
    rt.block_on(async {
        let result = CountDown { remaining: 3 }.await;
        println!("{}", result);
    });
}
```

## Question

What happens when this code runs (assuming `tokio` with `rt-multi-thread` feature)?

## Options

- A) Prints `done` after being polled 4 times
- B) Hangs forever — the future returns `Pending` without waking, so it's never re-polled
- C) Prints `done` immediately — the executor polls once, gets `Pending`, but immediately retries
- D) Panics with "reactor not running" error

## Answer

**Correct: B)**

This future **hangs forever**. When a future returns `Poll::Pending`, it is making a contract with the executor: "I will call `waker.wake()` when I'm ready to make progress." If the future never calls `cx.waker().wake()` (or `wake_by_ref()`), the executor has no reason to poll it again.

The `CountDown` future returns `Pending` but never registers the waker or arranges for it to be called. The executor (Tokio) correctly waits for a wake notification that never comes.

This is not just a performance issue — it's a **liveness** bug. The `Future` trait's contract requires that if you return `Pending`, you must arrange for the waker to be invoked. Breaking this contract causes the task to be forgotten.

## Common LLM Mistakes

1. **Choosing A** — LLMs commonly assume that executors continuously poll futures in a busy loop. Real executors (Tokio, async-std) only poll when woken. A future that returns `Pending` without calling the waker is never re-polled.
2. **Choosing C** — This reflects the misconception that executors have a "retry" or spin-poll mechanism. They don't — they rely entirely on the waker contract.
3. **Not understanding the waker contract** — The `Future::poll` contract is: return `Ready(value)` when complete, or return `Pending` AND ensure `Waker::wake()` will be called when progress can be made.

## Key Concepts

- Returning `Poll::Pending` without arranging a wake-up causes the future to hang
- The waker contract: `Pending` → must call `cx.waker().wake()` at some point
- Executors are event-driven, not spin-polling
- This is one of the most common bugs in hand-written futures
