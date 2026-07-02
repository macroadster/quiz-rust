# Problem 059: mpsc::Receiver::recv vs try_recv Semantics — Solution

## Answer

**Correct: A)**

Step by step:

1. **`r1 = rx.try_recv()`**: The spawned thread hasn't sent anything yet (it sleeps for 100ms). `try_recv()` is non-blocking and returns `Err(TryRecvError::Empty)` — the channel is still open but no messages are available.

2. **`r2 = rx.recv()`**: This call blocks until a message arrives. After ~100ms, the spawned thread sends `"hello"`, and `recv()` returns `Ok("hello")`.

3. **`r3 = rx.recv()`**: The spawned thread has dropped `tx` (it went out of scope after the send), so the channel is now disconnected. `recv()` immediately returns `Err(RecvError)` — it doesn't block because it knows no more messages can ever arrive.

Key distinctions:
- `try_recv()` returns `Err(Empty)` when the channel is open but empty; `Err(Disconnected)` when all senders are dropped
- `recv()` blocks when the channel is open and empty; returns `Err(RecvError)` immediately when disconnected and empty
- `recv()` does NOT hang on a disconnected channel — it can detect that no more messages will come

## Common LLM Mistakes

1. **Choosing C** — LLMs often assume `recv()` blocks forever if there are no messages. But `recv()` detects channel disconnection (all senders dropped) and returns `Err` immediately. It only blocks while the channel is *open* and empty.
2. **Choosing B** — The first `try_recv` returns `Empty`, not `Disconnected`. The sender is still alive (the thread hasn't dropped `tx` yet — it's sleeping). `Empty` vs `Disconnected` is a critical distinction.
3. **Not distinguishing TryRecvError::Empty from TryRecvError::Disconnected** — These are different error variants with different meanings: `Empty` means "channel open, no data yet"; `Disconnected` means "all senders dropped."

## Key Concepts

- `try_recv()`: non-blocking; returns `Empty` or `Disconnected`
- `recv()`: blocks until message or disconnection; returns `Err(RecvError)` on disconnection
- A disconnected channel with no pending messages immediately returns `Err` on `recv()`
- Channel disconnects when ALL senders are dropped (not when any single clone is dropped)
