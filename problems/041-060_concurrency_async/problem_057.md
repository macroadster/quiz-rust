# Problem 057: SeqCst vs Acquire/Release — When Does It Matter?

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Ordering`, `SeqCst`, `Acquire`, `Release`, `store-buffer`

## Problem Statement

Consider the classic "store-buffer" litmus test with two atomic variables:

```rust
use std::sync::atomic::{AtomicBool, Ordering};
use std::thread;

static X: AtomicBool = AtomicBool::new(false);
static Y: AtomicBool = AtomicBool::new(false);

fn main() {
    let t1 = thread::spawn(|| {
        X.store(true, Ordering::Release);
        Y.load(Ordering::Acquire)    // reads Y
    });

    let t2 = thread::spawn(|| {
        Y.store(true, Ordering::Release);
        X.load(Ordering::Acquire)    // reads X
    });

    let r1 = t1.join().unwrap();  // t1's read of Y
    let r2 = t2.join().unwrap();  // t2's read of X

    if !r1 && !r2 {
        println!("BOTH SAW FALSE");  // Can this print?
    }
}
```

## Question

Can `r1 == false && r2 == false` (both threads see the other's variable as `false`) occur with `Acquire`/`Release` ordering?

## Options

- A) No — `Acquire`/`Release` prevents this; at least one thread must see the other's store
- B) Yes — `Acquire`/`Release` only synchronizes through the *same* variable; since each thread writes and reads *different* variables, no happens-before is established
- C) No — this is impossible on any real hardware regardless of ordering
- D) Yes — but only with `Relaxed`; using `Acquire`/`Release` prevents it
