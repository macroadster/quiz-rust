# Problem 041: The Send Bound Surprise with Rc

**Difficulty:** ⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Send`, `Rc`, `Arc`, `thread::spawn`

## Problem Statement

A developer wants to share data across threads. Consider the following code:

```rust
use std::rc::Rc;
use std::thread;

fn main() {
    let data = Rc::new(vec![1, 2, 3]);
    let data_clone = Rc::clone(&data);

    let handle = thread::spawn(move || {
        println!("{:?}", data_clone);
    });

    println!("{:?}", data);
    handle.join().unwrap();
}
```

## Question

What happens when you try to compile this code?

## Options

- A) It compiles and prints `[1, 2, 3]` twice
- B) Compilation error: `Rc<Vec<i32>>` cannot be sent between threads safely because it doesn't implement `Send`
- C) Compilation error: `data` is moved into the closure and cannot be used afterward
- D) It compiles but panics at runtime due to a data race on the reference count

## Answer

**Correct: B)**

The compiler rejects this code because `Rc<T>` does not implement `Send`. The `thread::spawn` function requires its closure argument to be `Send`, since the closure will be executed on a different thread. `Rc` uses non-atomic reference counting, which would be unsound if shared across threads — two threads could simultaneously modify the reference count, leading to use-after-free bugs. The compiler enforces this statically.

Note that option C is a trap: while `data_clone` is moved into the closure via `move`, the original `data` is a *separate* `Rc` clone and remains valid. However, the `Send` bound error fires first and prevents compilation entirely.

## Common LLM Mistakes

1. **Choosing C** — LLMs sometimes focus on the `move` keyword and incorrectly conclude that `data` is moved. But `data_clone` is a separate `Rc::clone`, so `data` itself is not moved. However, this reasoning is moot because the `Send` bound error prevents compilation before any move analysis matters.
2. **Choosing D** — LLMs may know that `Rc` is not thread-safe but incorrectly conclude it's a runtime problem. Rust catches this at compile time through the `Send` trait bound.
3. **Overlooking that `Rc` is explicitly `!Send`** — `Rc` has a negative impl `impl<T> !Send for Rc<T>` which the compiler checks against `thread::spawn`'s `F: Send` bound.

## Key Concepts

- `Rc<T>` is explicitly `!Send` and `!Sync`
- `thread::spawn` requires `F: Send + 'static`
- `Arc<T>` is the thread-safe alternative that uses atomic reference counting
