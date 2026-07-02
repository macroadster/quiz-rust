# Problem 056: Relaxed Ordering and Visibility — Solution

## Answer

**Correct: C)**

With `Relaxed` ordering, the only guarantee is **atomicity** of each individual operation — no tearing. There is **no** guarantee about the ordering in which different atomic variables' updates become visible to other threads.

Even though the writer thread stores `DATA` before `FLAG` in program order, `Relaxed` does not establish a *happens-before* relationship between these stores from the perspective of other threads. The reader thread may observe `FLAG == true` while still seeing the stale value `DATA == 0`.

To fix this, use `Release` ordering on the stores and `Acquire` ordering on the loads:
```rust
DATA.store(42, Ordering::Release);      // or at minimum the FLAG store
FLAG.store(true, Ordering::Release);
// ...
while !FLAG.load(Ordering::Acquire) {}  // establishes happens-before
DATA.load(Ordering::Acquire)            // guaranteed to see 42
```

The `Release`/`Acquire` pair on `FLAG` creates a *happens-before* edge: if the `Acquire` load sees the value from the `Release` store, all writes before the `Release` are visible to code after the `Acquire`.

## Common LLM Mistakes

1. **Choosing B** — This is the most common mistake. While stores happen in program order *within the writing thread*, `Relaxed` ordering does not guarantee that other threads see them in that order. This is about cross-thread visibility, not execution order.
2. **Choosing D** — While x86 has a stronger memory model (Total Store Order) that makes this bug harder to observe in practice, the Rust memory model is architecture-independent. The C++20/Rust abstract machine allows this reordering, so it's a bug regardless of architecture. Compilers may also reorder Relaxed atomics.
3. **Choosing A** — Only `SeqCst` provides sequential consistency. `Relaxed` is explicitly the weakest ordering and provides only per-variable atomicity.

## Key Concepts

- `Relaxed` ordering guarantees atomicity but no inter-variable ordering
- `Release`/`Acquire` pairs establish happens-before relationships
- Rust's memory model is architecture-independent — don't rely on x86's strong ordering
- The classic flag-based pattern requires at minimum `Release` store + `Acquire` load on the flag
