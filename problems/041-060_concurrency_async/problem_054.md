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

## Answer

**Correct: B)**

This code **deadlocks**. `std::sync::Mutex` is **not re-entrant** (also called "non-recursive"). When the thread calls `m.lock()` the second time, it blocks waiting for the lock to be released. But the lock is held by the same thread (via `guard1`), which is now blocked. This creates a deadlock.

Key facts about `std::sync::Mutex`:
- It is **not** re-entrant. A thread cannot acquire the same mutex twice.
- There is no re-entrant mutex in the standard library. If you need one, use `parking_lot::ReentrantMutex` or similar.
- The behavior on double-locking from the same thread is technically *platform-dependent* but will deadlock on all common platforms (Linux pthreads `PTHREAD_MUTEX_DEFAULT`, Windows `SRWLOCK`).

The compiler cannot detect this because lock ordering is a runtime property. No static analysis in the type system prevents it.

## Common LLM Mistakes

1. **Choosing A** — LLMs commonly conflate `std::sync::Mutex` with re-entrant mutexes from other languages (like Java's `synchronized` or C++'s `std::recursive_mutex`). Rust's standard mutex is NOT re-entrant.
2. **Choosing C** — While some mutex implementations *do* panic on double-locking (the Rust docs say behavior may vary), the standard behavior is deadlock, not panic. The default pthread mutex type blocks without checking for the double-lock.
3. **Choosing D** — The compiler does not analyze lock patterns. Deadlock detection is not part of Rust's type system or borrow checker.

## Key Concepts

- `std::sync::Mutex` is not re-entrant — locking from the same thread deadlocks
- No re-entrant mutex in Rust's standard library
- Deadlock is a runtime issue — the compiler cannot detect it
- Always use scoped locks and structured lock ordering to avoid deadlocks
