# Problem 056: Relaxed Ordering and Visibility

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `AtomicBool`, `Ordering`, `Relaxed`, `Acquire`, `Release`

## Problem Statement

A developer uses atomics with `Relaxed` ordering for a flag-based communication pattern:

```rust
use std::sync::atomic::{AtomicBool, AtomicI32, Ordering};
use std::thread;

static DATA: AtomicI32 = AtomicI32::new(0);
static FLAG: AtomicBool = AtomicBool::new(false);

fn main() {
    let writer = thread::spawn(|| {
        DATA.store(42, Ordering::Relaxed);
        FLAG.store(true, Ordering::Relaxed);
    });

    let reader = thread::spawn(|| {
        while !FLAG.load(Ordering::Relaxed) {
            // spin
        }
        DATA.load(Ordering::Relaxed)
    });

    writer.join().unwrap();
    let value = reader.join().unwrap();
    println!("{}", value);
}
```

## Question

Is it possible for this program to print `0` (i.e., the reader sees `FLAG == true` but `DATA == 0`)?

## Options

- A) No — atomic operations are always sequentially consistent regardless of ordering
- B) No — `store` operations in a single thread are always seen in order by other threads
- C) Yes — `Relaxed` ordering provides no inter-variable ordering guarantees; the reader may see the flag set but the data not yet written
- D) Yes — but only on ARM/PowerPC architectures, never on x86

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
