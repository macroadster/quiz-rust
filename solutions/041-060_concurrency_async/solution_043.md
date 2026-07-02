# Problem 043: Sync Does Not Imply Send — Solution

## Answer

**Correct: C)**

`MutexGuard<'_, T>` is `Sync` (when `T: Sync`) but is **not** `Send`. This is one of the clearest examples showing that `Sync` does not imply `Send`.

- **Line A compiles**: `&MutexGuard` requires `MutexGuard: Sync`. Since `MutexGuard<'_, i32>` implements `Sync` (because `i32: Sync`), this works.
- **Line B fails**: `MutexGuard` is explicitly `!Send`. This is because unlocking a mutex must happen on the same thread that locked it on certain platforms (notably pthreads). If you could send a `MutexGuard` to another thread, that thread could drop it (unlocking the mutex), violating this invariant.

## Common LLM Mistakes

1. **Choosing A** — LLMs often assume that if a type wraps a `Send + Sync` value, the wrapper must also be `Send`. `MutexGuard` is a deliberate counterexample: it wraps data that is `Send + Sync` but the guard itself is `!Send`.
2. **Choosing D** — Some LLMs incorrectly believe `MutexGuard` is neither `Send` nor `Sync`. In fact, it *is* `Sync` — multiple threads can hold shared references to the same guard (they'd all see the same locked data).
3. **Confusing the relationship** — The general rule "`&T` is `Send` iff `T` is `Sync`" is correct, but it doesn't mean `T` being `Sync` implies `T` is `Send`. These are independent properties.

## Key Concepts

- `MutexGuard<'_, T>` is `Sync` (if `T: Sync`) but explicitly `!Send`
- `Send` and `Sync` are independent traits — neither implies the other
- The relationship is: `T: Sync` ⟺ `&T: Send`
- Mutex unlock must occur on the locking thread (platform constraint)
