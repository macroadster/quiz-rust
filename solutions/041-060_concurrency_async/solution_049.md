# Problem 049: Waker Cloning and Wake Semantics — Solution

## Answer

**Correct: C)**

Calling `.wake()` on a previously-cloned waker is **safe** (no UB), but it's **incorrect behavior** in general. The `Waker` contract states:

1. Wakers must be safe to clone and wake from any thread.
2. Waking a stale waker won't cause UB, but it may notify the wrong executor task slot or cause spurious wake-ups.
3. Executors *may* change the waker between polls (e.g., when a task migrates between worker threads). A well-behaved future should always use or store the **most recent** waker from `cx.waker()`.

The correct pattern is: on every `poll`, check if the stored waker `will_wake(cx.waker())`. If not, replace it. `Waker::will_wake()` is the API for this purpose.

The code above does this correctly by replacing `stashed_waker` on every poll — but if it were to skip the replacement and use a stale waker, it would be a logic bug, not a safety violation.

## Common LLM Mistakes

1. **Choosing A** — While it's technically memory-safe, calling `.wake()` on a stale waker is not "harmless." It can lead to waking a task that no longer exists in the executor's queue, causing spurious polls or, in pathological cases, tasks that never wake up.
2. **Choosing B** — Stale wakers are not "invalidated" in a UB sense. Rust's safety model ensures that calling wake on any valid `Waker` clone is safe. The issue is semantic correctness, not memory safety.
3. **Choosing D** — `Waker` absolutely implements `Clone`. The `RawWakerVTable` includes a clone function pointer for this purpose.

## Key Concepts

- `Waker` implements `Clone` and `Send + Sync`
- Stale wakers don't cause UB but may cause incorrect behavior
- `Waker::will_wake()` checks if two wakers point to the same task
- Always update stored wakers on each poll using the fresh `cx.waker()`
