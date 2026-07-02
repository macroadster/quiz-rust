# Problem 060: Tokio block_on Nesting and Runtime Context

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `tokio`, `block_on`, `spawn`, `runtime-context`, `nested-runtime`

## Problem Statement

A developer tries to nest Tokio runtimes and use `block_on` from within an async context:

```rust
use tokio::runtime::Runtime;

async fn inner_work() -> i32 {
    tokio::time::sleep(std::time::Duration::from_millis(10)).await;
    42
}

async fn outer_work() {
    // Attempt to create a new runtime inside an async context
    let rt = Runtime::new().unwrap();
    let result = rt.block_on(inner_work());  // Line X
    println!("Result: {}", result);
}

#[tokio::main]
async fn main() {
    outer_work().await;
}
```

## Question

What happens when this code runs?

## Options

- A) Prints `Result: 42` — creating nested runtimes is fine
- B) Panics at Line X with "Cannot start a runtime from within a runtime"
- C) Deadlocks — the inner `block_on` blocks the outer runtime's thread
- D) Compilation error — `block_on` cannot be called inside an async function
