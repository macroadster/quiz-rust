# Problem 054: Deadlock with Nested Mutex Locks

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Mutex`, `deadlock`, `reentrancy`, `lock-ordering`

## Problem Statement

A developer writes code that acquires the same `std::sync::Mutex` twice on the same thread:

```rust
use std::sync::Mutex;

fn main() {
    let m = Mutex::new(42);

    let guard1 = m.lock().unwrap();
    println!("First lock: {}", *guard1);

    let guard2 = m.lock().unwrap();  // Line X
    println!("Second lock: {}", *guard2);
}
```

## Question

What happens when this code runs?

## Options

- A) Prints both values — `std::sync::Mutex` is re-entrant, so the same thread can lock it twice
- B) Deadlocks at Line X — `std::sync::Mutex` is NOT re-entrant, and the thread blocks waiting for a lock it already holds
- C) Panics at Line X — the mutex detects the double-lock and panics
- D) Compilation error — the compiler detects the double lock and prevents it
