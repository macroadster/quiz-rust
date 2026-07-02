# Problem 065: mem::forget and Drop Safety

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `mem-forget`, `drop`, `safety`

## Problem Statement

A developer uses `mem::forget` and wonders about its safety classification.

```rust
use std::mem;

struct Guard {
    name: String,
}

impl Drop for Guard {
    fn drop(&mut self) {
        println!("dropping: {}", self.name);
    }
}

fn main() {
    let g1 = Guard { name: String::from("alpha") };
    let g2 = Guard { name: String::from("beta") };

    mem::forget(g1);
    println!("after forget");
    drop(g2);
    println!("after drop");
}
```

## Question

What is the output of this program?

## Options

- A) `dropping: alpha` / `after forget` / `dropping: beta` / `after drop`
- B) `after forget` / `after drop` / `dropping: beta`
- C) `after forget` / `dropping: beta` / `after drop`
- D) Compilation error — `mem::forget` requires `unsafe` because it skips destructors
