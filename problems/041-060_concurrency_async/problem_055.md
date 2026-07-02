# Problem 055: tokio::sync::Mutex vs std::sync::Mutex in Async

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `tokio::Mutex`, `std::Mutex`, `async`, `await`, `Send`

## Problem Statement

Compare these two async functions:

```rust
use std::sync::Arc;

// Version 1: std::sync::Mutex
async fn version1() {
    let data = Arc::new(std::sync::Mutex::new(vec![]));
    let data_clone = Arc::clone(&data);

    tokio::spawn(async move {
        let mut guard = data_clone.lock().unwrap();
        guard.push(1);
        drop(guard);  // explicit drop before .await
        tokio::time::sleep(std::time::Duration::from_millis(10)).await;
    });
}

// Version 2: tokio::sync::Mutex
async fn version2() {
    let data = Arc::new(tokio::sync::Mutex::new(vec![]));
    let data_clone = Arc::clone(&data);

    tokio::spawn(async move {
        let mut guard = data_clone.lock().await;
        guard.push(1);
        // no explicit drop — guard held across .await
        tokio::time::sleep(std::time::Duration::from_millis(10)).await;
        guard.push(2);
    });
}
```

## Question

Which version(s) compile successfully?

## Options

- A) Both compile — `tokio::sync::Mutex` and `std::sync::Mutex` are interchangeable in async contexts
- B) Version 1 compiles; Version 2 fails because `tokio::sync::MutexGuard` is also `!Send`
- C) Both compile — Version 1 works because the `std::sync::MutexGuard` is dropped before `.await`; Version 2 works because `tokio::sync::MutexGuard` is `Send`
- D) Version 1 fails because `std::sync::Mutex` cannot be used in async code; Version 2 compiles
