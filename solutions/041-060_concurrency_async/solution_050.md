# Problem 050: Borrowing Across .await Points — Solution

## Answer

**Correct: B)**

This is a critical subtlety in async Rust. When an async function is spawned with `tokio::spawn`, the resulting future must be `Send` (because it might be moved between worker threads at `.await` points).

In `process`: The `guard` is explicitly dropped before the `.await`. The compiler's analysis determines that `MutexGuard` (which is `!Send`) does **not** live across the `.await` point, so the generated future is `Send`.

In `process_v2`: The `guard` is not dropped until the end of its scope. Even though the compiler *could* theoretically see it's not used after the `.await`, the current borrow checker considers `guard` alive until the end of the block. Since `MutexGuard` is `!Send` and it lives across the `.await` point, the generated future is `!Send`, and `tokio::spawn` rejects it.

The fix: either explicitly `drop(guard)` before the `.await`, or introduce a block scope `{ let guard = ...; guard.push(1); }` before the `.await`.

## Common LLM Mistakes

1. **Choosing A** — LLMs frequently assume the compiler optimizes away unused variables before `.await`. The current compiler analysis is conservative: if a `!Send` type is in scope across `.await`, the future is `!Send`, even if the variable isn't used after the yield point.
2. **Choosing D** — While `process_v2` *would* deadlock (since `std::sync::Mutex` is not re-entrant), the compilation error about `Send` fires before you ever reach runtime.
3. **Not understanding that .await is a yield point** — The generated state machine must save all live variables across `.await`. If any of them are `!Send`, the entire future is `!Send`.

## Key Concepts

- `tokio::spawn` requires `Send` futures
- `MutexGuard` is `!Send` — it cannot be held across `.await` in spawned tasks
- Explicit `drop()` before `.await` prevents `!Send` contamination
- The compiler is conservative: scope, not usage, determines liveness across `.await`
