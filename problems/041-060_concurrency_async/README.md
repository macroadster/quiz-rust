# Category 3: Concurrency & Async

20 problems testing understanding of Rust's concurrency and async model.

## Topics Covered

- **Send/Sync bounds** — when types are/aren't Send or Sync (041–043)
- **Pin and Unpin** — self-referential futures, structural pinning (044–046)
- **Future trait internals** — hand-rolled futures, waker semantics (047–049)
- **Async lifetime issues** — borrowing across `.await`, async trait limits (050–052)
- **Mutex/RwLock gotchas** — poisoning, deadlocks, guards across await (053–055)
- **Atomic ordering** — Relaxed vs Acquire/Release vs SeqCst (056–057)
- **Channel semantics** — mpsc send/recv edge cases (058–059)
- **Tokio-specific pitfalls** — spawn, block_on nesting (060)
