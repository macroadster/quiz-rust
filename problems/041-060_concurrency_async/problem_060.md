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

## Answer

**Correct: B)**

This panics with the error: **"Cannot start a runtime from within a runtime. This happens because a function (like `block_on`) attempted to block the current thread while the thread is being used to drive asynchronous tasks."**

Tokio uses thread-local storage to track whether the current thread is already inside a runtime. When `block_on` is called, it checks this flag and panics if a runtime context already exists on the current thread.

The `#[tokio::main]` macro creates a runtime and calls `block_on(main())`. Inside that, `outer_work` tries to call `rt.block_on()`, which detects the existing runtime context and panics.

This is by design — `block_on` blocks the current thread, which would prevent the outer runtime from making progress on its tasks, potentially causing deadlocks.

**Valid alternatives:**
- Use `tokio::spawn` to run work on the existing runtime
- Use `tokio::task::spawn_blocking` with a new runtime inside the blocking thread
- Use `Handle::current()` to spawn on the existing runtime
- Restructure to avoid nesting — just `.await` the async work directly

## Common LLM Mistakes

1. **Choosing C** — While deadlock is the *reason* Tokio prevents this, the actual behavior is a panic, not a silent deadlock. Tokio explicitly detects and panics on this pattern rather than allowing it to deadlock.
2. **Choosing A** — LLMs sometimes assume runtimes can nest freely. They cannot in Tokio. The thread-local runtime context detection prevents this.
3. **Choosing D** — There's no compilation error. `block_on` is a regular method on `Runtime` and can syntactically be called anywhere. The error is a runtime panic, not a compile-time check.

## Key Concepts

- Tokio's `block_on` panics if called from within an existing runtime context
- Tokio uses thread-local storage to detect nested runtime usage
- `spawn_blocking` can create an escape hatch to use a new runtime in a non-async thread
- The correct pattern in async code is to `.await` or `tokio::spawn`, not `block_on`
