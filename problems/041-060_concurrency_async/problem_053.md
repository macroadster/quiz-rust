# Problem 053: Mutex Poisoning Semantics

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Mutex`, `poisoning`, `PoisonError`, `panic`

## Problem Statement

Consider this code where a thread panics while holding a mutex:

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let data = Arc::new(Mutex::new(vec![1, 2, 3]));
    let data2 = Arc::clone(&data);

    let handle = thread::spawn(move || {
        let mut guard = data2.lock().unwrap();
        guard.push(4);
        panic!("oops");
    });

    let _ = handle.join(); // We ignore the thread's panic result

    // Try to access the mutex from the main thread
    match data.lock() {
        Ok(guard) => println!("Ok: {:?}", *guard),
        Err(poisoned) => {
            let guard = poisoned.into_inner();
            println!("Poisoned: {:?}", *guard);
        }
    }
}
```

## Question

What does this program print (ignoring the panic message from the spawned thread)?

## Options

- A) `Ok: [1, 2, 3, 4]`
- B) `Poisoned: [1, 2, 3, 4]`
- C) `Poisoned: [1, 2, 3]` — the push is rolled back because the thread panicked
- D) The program panics on `data.lock()` because the mutex is destroyed after poisoning

## Answer

**Correct: B)**

The output is `Poisoned: [1, 2, 3, 4]`. Key points:

1. **The push happened**: `guard.push(4)` executes before `panic!("oops")`. The data was mutated.
2. **Poisoning preserves data**: When a thread panics while holding a `MutexGuard`, the mutex is marked as "poisoned." But the data is still there — Rust doesn't have transactions or rollbacks.
3. **`into_inner()` recovers the data**: `PoisonError::into_inner()` gives you the `MutexGuard`, allowing access to the (potentially inconsistent) data inside.

This is by design. Mutex poisoning is a **notification mechanism**, not a data protection mechanism. It tells you "a thread panicked while holding this lock, so the data might be in an inconsistent state." But it lets you decide what to do — you can recover the data if you know it's still valid.

## Common LLM Mistakes

1. **Choosing C** — Rust does not have automatic rollback/undo semantics. `panic!` unwinds the stack and runs destructors, but it doesn't undo mutations that already occurred. The `push(4)` happened before the panic.
2. **Choosing D** — A poisoned mutex is not destroyed or unusable. `lock()` returns `Err(PoisonError)`, but you can extract the guard from it and continue.
3. **Choosing A** — The mutex IS poisoned because a thread panicked while holding the lock. `lock()` returns `Err`, not `Ok`. LLMs sometimes forget that a panic during guard holding poisons the mutex.

## Key Concepts

- Mutex poisoning occurs when a thread panics while holding a `MutexGuard`
- Poisoning doesn't destroy or clear the data — mutations are preserved
- `PoisonError::into_inner()` recovers the `MutexGuard` and its data
- Poisoning is a best-effort consistency check, not a transaction mechanism
