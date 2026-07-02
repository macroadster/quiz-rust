# Problem 058: mpsc Channel — Send After All Receivers Dropped — Solution

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
