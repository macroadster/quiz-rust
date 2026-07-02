# Problem 055: tokio::sync::Mutex vs std::sync::Mutex in Async — Solution

## Answer

**Correct: C)**

Both versions compile, but for different reasons:

**Version 1**: `std::sync::MutexGuard` is `!Send`, which would prevent the spawned future from being `Send`. However, because `drop(guard)` is called before the `.await`, the guard does not exist across the yield point. The compiler sees that no `!Send` type is alive at the `.await`, so the future is `Send`.

**Version 2**: `tokio::sync::MutexGuard` **is `Send`** (unlike `std::sync::MutexGuard`). This is a deliberate design choice by Tokio. Because it's `Send`, the guard can be held across `.await` points without making the future `!Send`. This is the primary advantage of `tokio::sync::Mutex` over `std::sync::Mutex` in async code.

The general guidance is:
- Use `std::sync::Mutex` for short, non-async critical sections (faster, no overhead)
- Use `tokio::sync::Mutex` when you need to hold the lock across `.await` points

## Common LLM Mistakes

1. **Choosing B** — A critical mistake. `tokio::sync::MutexGuard` IS `Send`. This is the *entire point* of `tokio::sync::Mutex` — to provide a mutex whose guard can be held across `.await` in spawned tasks.
2. **Choosing D** — `std::sync::Mutex` works perfectly fine in async code. It's actually *preferred* for short critical sections because it's synchronous and faster. The restriction is only about holding the guard across `.await` in spawned tasks.
3. **Believing `std::sync::Mutex` is forbidden in async** — It's not. The Tokio documentation explicitly recommends `std::sync::Mutex` for quick lock-and-modify patterns that don't span `.await`.

## Key Concepts

- `std::sync::MutexGuard` is `!Send`; `tokio::sync::MutexGuard` is `Send`
- `std::sync::Mutex` is fine in async code if guards don't cross `.await` points
- `tokio::sync::Mutex` is designed for holding locks across `.await`
- `std::sync::Mutex` is faster for non-async critical sections — prefer it when possible
