# Problem 044: Pinning and Moving — The Fundamental Contract

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Pin`, `Unpin`, `move`, `self-referential`

## Problem Statement

Consider the following code that uses `Pin` with different types:

```rust
use std::pin::Pin;

fn main() {
    let mut s = String::from("hello");
    let mut pinned = Pin::new(&mut s);

    // Replace the pinned string
    *pinned.as_mut() = String::from("world");

    println!("{}", pinned);
}
```

## Question

Does this code compile and run? If so, what is the output?

## Options

- A) Compilation error: cannot mutate pinned data
- B) Compilation error: `String` does not implement `Unpin`
- C) Compiles and prints `world`
- D) Compiles and prints `hello`

## Answer

**Correct: C)**

This compiles and prints `world`. The key insight is that `String` implements `Unpin`. When a type implements `Unpin`, `Pin` provides **no additional guarantees** beyond a normal mutable reference. `Pin::new()` is available only for types that implement `Unpin` — its signature is `fn new(pointer: P) -> Pin<P> where P::Target: Unpin`.

Since `String: Unpin`, we can freely call `as_mut()` on the `Pin<&mut String>` and mutate the underlying data, including replacing it entirely. The `Pin` wrapper is essentially a no-op for `Unpin` types.

Most standard library types implement `Unpin`. The primary types that are `!Unpin` are compiler-generated futures (from `async` blocks/functions) and types that explicitly opt out.

## Common LLM Mistakes

1. **Choosing A** — LLMs often assume `Pin` prevents all mutation. `Pin` only prevents *moving* the pinned value out, and even that restriction only matters for `!Unpin` types. For `Unpin` types, `Pin` has no effect.
2. **Choosing B** — `String` absolutely implements `Unpin`. Almost all standard library types do. LLMs sometimes confuse "pinning" with "immutability" and assume types must opt-in to be usable with `Pin`.
3. **Misunderstanding Pin's purpose** — `Pin` exists specifically for self-referential types (like futures). For regular types that are `Unpin`, `Pin<&mut T>` is interchangeable with `&mut T`.

## Key Concepts

- `Pin<P>` only restricts movement for `!Unpin` types
- `String`, `Vec`, `i32`, and most standard types implement `Unpin`
- `Pin::new()` requires `T: Unpin` — it cannot even be called for `!Unpin` types
- `Pin` is meaningful primarily for self-referential structs and async futures
