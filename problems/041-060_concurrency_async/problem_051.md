# Problem 051: Async Closures and Lifetime Capture

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `async`, `closures`, `lifetimes`, `higher-rank`

## Problem Statement

A developer tries to write a function that accepts an async closure:

```rust
use std::future::Future;

async fn with_value<F, Fut>(f: F)
where
    F: FnOnce(&str) -> Fut,
    Fut: Future<Output = usize>,
{
    let s = String::from("hello");
    let result = f(&s).await;
    println!("{}", result);
}

async fn main_logic() {
    with_value(|s| async move {
        s.len()
    }).await;
}

#[tokio::main]
async fn main() {
    main_logic().await;
}
```

## Question

Does `main_logic` compile?

## Options

- A) Yes, compiles and prints `5`
- B) Compilation error: the closure's async block captures `s` with a lifetime that doesn't outlive the returned future
- C) Compilation error: `with_value` needs higher-ranked trait bounds (`for<'a>`) to accept closures that borrow their arguments
- D) Compilation error: `async move` takes ownership of `s`, but `s` is a reference so the future can't be `'static`
