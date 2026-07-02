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

## Answer

**Correct: B)**

When the receiver (`rx`) is dropped, the channel becomes "disconnected." Any subsequent `send()` on any sender cloned from the same channel returns `Err(SendError(value))`, where `value` is the data that was being sent.

`SendError<T>` is a struct `SendError(pub T)` — it wraps the value that failed to send, allowing the caller to recover it. So `e1.0` gives back `1` and `e2.0` gives back `2`.

Key semantics of `std::sync::mpsc`:
- `send()` returns `Err(SendError(val))` if no receiver exists — the value is returned to the caller
- Both `tx` and `tx2` are clones pointing to the same channel — both detect the disconnection
- The channel is disconnected the moment the last `Receiver` is dropped
- `send()` never panics — it returns `Result`

## Common LLM Mistakes

1. **Choosing A** — LLMs sometimes confuse MPSC channel semantics with fire-and-forget patterns. Rust's `send` explicitly checks for disconnection and returns the unsent value on failure.
2. **Choosing C** — `send()` does not panic. It returns `Result`. The `unwrap()` pattern (`tx.send(val).unwrap()`) is what would panic, but that's the caller's choice.
3. **Choosing D** — `SendError` is defined as `pub struct SendError<T>(pub T)` — a tuple struct with a public field. Accessing `.0` is valid.

## Key Concepts

- `mpsc::Sender::send()` returns `Err(SendError(T))` when the channel is disconnected
- `SendError` contains the unsent value, allowing recovery
- A channel disconnects when all receivers (or all senders) are dropped
- `send()` never panics — always returns `Result`
