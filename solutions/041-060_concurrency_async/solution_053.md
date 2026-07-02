# Problem 053: Mutex Poisoning Semantics — Solution

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
