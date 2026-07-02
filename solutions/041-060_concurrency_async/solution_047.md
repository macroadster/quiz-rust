# Problem 047: Hand-Rolling a Future — Poll Semantics — Solution

## Answer

**Correct: B)**

This future **hangs forever**. When a future returns `Poll::Pending`, it is making a contract with the executor: "I will call `waker.wake()` when I'm ready to make progress." If the future never calls `cx.waker().wake()` (or `wake_by_ref()`), the executor has no reason to poll it again.

The `CountDown` future returns `Pending` but never registers the waker or arranges for it to be called. The executor (Tokio) correctly waits for a wake notification that never comes.

This is not just a performance issue — it's a **liveness** bug. The `Future` trait's contract requires that if you return `Pending`, you must arrange for the waker to be invoked. Breaking this contract causes the task to be forgotten.

## Common LLM Mistakes

1. **Choosing A** — LLMs commonly assume that executors continuously poll futures in a busy loop. Real executors (Tokio, async-std) only poll when woken. A future that returns `Pending` without calling the waker is never re-polled.
2. **Choosing C** — This reflects the misconception that executors have a "retry" or spin-poll mechanism. They don't — they rely entirely on the waker contract.
3. **Not understanding the waker contract** — The `Future::poll` contract is: return `Ready(value)` when complete, or return `Pending` AND ensure `Waker::wake()` will be called when progress can be made.

## Key Concepts

- Returning `Poll::Pending` without arranging a wake-up causes the future to hang
- The waker contract: `Pending` → must call `cx.waker().wake()` at some point
- Executors are event-driven, not spin-polling
- This is one of the most common bugs in hand-written futures
