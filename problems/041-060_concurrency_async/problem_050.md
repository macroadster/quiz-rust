# Problem 050: Borrowing Across .await Points

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `async`, `await`, `borrowing`, `Send`, `lifetimes`

## Problem Statement

Consider this async function that tries to hold a reference across an `.await`:

```rust
use std::sync::Mutex;

async fn process(mutex: &Mutex<Vec<i32>>) {
    let mut guard = mutex.lock().unwrap();
    guard.push(1);
    drop(guard);

    tokio::time::sleep(std::time::Duration::from_millis(10)).await;

    let mut guard2 = mutex.lock().unwrap();
    guard2.push(2);
}

#[tokio::main]
async fn main() {
    let m = Mutex::new(vec![]);
    process(&m).await;
    println!("{:?}", m.lock().unwrap());
}
```

Now consider this subtle variation:

```rust
async fn process_v2(mutex: &Mutex<Vec<i32>>) {
    let mut guard = mutex.lock().unwrap();
    guard.push(1);
    // NOTE: no explicit drop(guard) here

    tokio::time::sleep(std::time::Duration::from_millis(10)).await;

    let mut guard2 = mutex.lock().unwrap();
    guard2.push(2);
}
```

## Question

What is the difference between `process` and `process_v2` when used with `tokio::spawn`?

## Options

- A) Both work identically — the compiler drops `guard` before the `.await` in both cases
- B) `process` compiles with `tokio::spawn`; `process_v2` does not because `MutexGuard` (which is `!Send`) is held across an `.await` point
- C) Both fail to compile with `tokio::spawn` because `MutexGuard` is in scope
- D) `process_v2` deadlocks at runtime but compiles fine
