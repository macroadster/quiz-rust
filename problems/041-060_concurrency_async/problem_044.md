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
