# Problem 057: SeqCst vs Acquire/Release — When Does It Matter? — Solution

## Answer

**Correct: B)**

This is the classic **store-buffer** or **IRIW** (Independent Reads of Independent Writes) litmus test. With `Acquire`/`Release` ordering, the answer is **yes**, both threads can see `false`.

`Acquire`/`Release` creates a happens-before relationship only when one thread's `Release` store is observed by another thread's `Acquire` load **on the same atomic variable**. In this code:
- `t1` stores to `X` (Release) and loads from `Y` (Acquire)
- `t2` stores to `Y` (Release) and loads from `X` (Acquire)

Since `t1`'s store is to `X` but its load is from `Y`, and vice versa, no happens-before edge is formed. Each thread may independently fail to see the other's store.

Only `SeqCst` ordering prevents this outcome, because `SeqCst` establishes a **single total order** of all `SeqCst` operations visible to all threads.

## Common LLM Mistakes

1. **Choosing A** — This is the most critical mistake. `Acquire`/`Release` does NOT provide a total ordering. It only synchronizes pairwise through the same variable. The store-buffer anomaly is the canonical example showing where `SeqCst` is necessary.
2. **Choosing C** — On ARM, POWER, and RISC-V, this reordering is observable in practice. Even on x86, while store-buffer reordering is possible, it's rare — but the memory model permits it.
3. **Conflating Acquire/Release with SeqCst** — This is precisely the scenario that distinguishes them. `SeqCst` provides the single total order that prevents this anomaly.

## Key Concepts

- `Acquire`/`Release` synchronizes only through the same atomic variable
- `SeqCst` establishes a single total order across ALL `SeqCst` operations
- The store-buffer litmus test is the canonical example requiring `SeqCst`
- In practice, most algorithms can use `Acquire`/`Release` — `SeqCst` is rarely needed
