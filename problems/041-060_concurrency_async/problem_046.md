# Problem 046: Pinning a Future to the Stack

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Pin`, `Unpin`, `async`, `stack-pinning`

## Problem Statement

A developer tries to pin a future to the stack and poll it manually:

```rust
use std::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll, Waker, RawWaker, RawWakerVTable};

fn dummy_waker() -> Waker {
    fn no_op(_: *const ()) {}
    fn clone(p: *const ()) -> RawWaker { RawWaker::new(p, &VTABLE) }
    static VTABLE: RawWakerVTable = RawWakerVTable::new(clone, no_op, no_op, no_op);
    unsafe { Waker::from_raw(RawWaker::new(std::ptr::null(), &VTABLE)) }
}

async fn compute() -> i32 { 42 }

fn main() {
    let fut = compute();
    let mut pinned = Pin::new(&mut fut);  // Line X
    let waker = dummy_waker();
    let mut cx = Context::from_waker(&waker);
    match pinned.as_mut().poll(&mut cx) {
        Poll::Ready(v) => println!("{}", v),
        Poll::Pending => println!("pending"),
    }
}
```

## Question

Does this code compile? If not, what is the error?

## Options

- A) Compiles and prints `42`
- B) Compilation error at Line X: the future returned by `compute()` is `!Unpin`, so `Pin::new()` cannot be used
- C) Compilation error: `fut` must be declared `mut`
- D) Compilation error: `Pin::new` expects an owned value, not a reference
