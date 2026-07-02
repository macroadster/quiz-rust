# Problem 058: mpsc Channel — Send After All Receivers Dropped

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `mpsc`, `channel`, `SendError`, `disconnected`

## Problem Statement

Consider this code using `std::sync::mpsc`:

```rust
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();
    let tx2 = tx.clone();

    drop(rx);  // Drop the only receiver

    let result1 = tx.send(1);
    let result2 = tx2.send(2);

    match (&result1, &result2) {
        (Err(e1), Err(e2)) => println!("Both failed: {} {}", e1.0, e2.0),
        (Ok(()), Err(e))   => println!("First ok, second failed: {}", e.0),
        (Err(e), Ok(()))   => println!("First failed, second ok: {}", e.0),
        (Ok(()), Ok(()))   => println!("Both ok"),
    }
}
```

## Question

What does this program print?

## Options

- A) `Both ok` — sending to a channel with no receiver is valid; messages are just dropped
- B) `Both failed: 1 2` — sending returns `Err(SendError(value))` when no receiver exists
- C) Panics at `tx.send(1)` — sending to a disconnected channel panics
- D) Compilation error — `SendError` doesn't have a `.0` field
