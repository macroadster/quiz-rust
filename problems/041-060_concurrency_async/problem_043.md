# Problem 043: Sync Does Not Imply Send

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Send`, `Sync`, `MutexGuard`, `trait-bounds`

## Problem Statement

Consider the relationship between `Send` and `Sync`. The following code creates a `MutexGuard` and attempts to use it across threads:

```rust
use std::sync::Mutex;
use std::thread;

fn require_send<T: Send>(_t: T) {}
fn require_sync<T: Sync>(_t: T) {}

fn main() {
    let m = Mutex::new(42);
    let guard = m.lock().unwrap();

    require_sync(&guard);  // Line A
    require_send(guard);   // Line B
}
```

## Question

Which lines fail to compile?

## Options

- A) Both lines compile successfully
- B) Line A fails (`MutexGuard` is not `Sync`), Line B compiles
- C) Line A compiles, Line B fails (`MutexGuard` is not `Send`)
- D) Both lines fail to compile
