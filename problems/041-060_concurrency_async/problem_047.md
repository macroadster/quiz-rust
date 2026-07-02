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
