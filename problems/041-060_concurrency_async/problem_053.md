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
