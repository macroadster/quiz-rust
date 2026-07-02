# Problem 059: mpsc::Receiver::recv vs try_recv Semantics

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `mpsc`, `channel`, `recv`, `try_recv`, `TryRecvError`

## Problem Statement

Consider this code that uses both `recv` and `try_recv`:

```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        thread::sleep(Duration::from_millis(100));
        tx.send("hello").unwrap();
        // tx is dropped here — channel disconnects after send
    });

    // Attempt 1: non-blocking try_recv
    let r1 = rx.try_recv();

    // Attempt 2: blocking recv
    let r2 = rx.recv();

    // Attempt 3: blocking recv again after channel is disconnected
    let r3 = rx.recv();

    println!("r1: {:?}", r1);
    println!("r2: {:?}", r2);
    println!("r3: {:?}", r3);
}
```

## Question

What is the output?

## Options

- A) `r1: Err(Empty)`, `r2: Ok("hello")`, `r3: Err(RecvError)`
- B) `r1: Err(Disconnected)`, `r2: Err(RecvError)`, `r3: Err(RecvError)`
- C) `r1: Err(Empty)`, `r2: Ok("hello")`, `r3` hangs forever
- D) `r1: Ok("hello")`, `r2: Err(RecvError)`, `r3: Err(RecvError)`
