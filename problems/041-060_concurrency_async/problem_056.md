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
