# Problem 018: Drop Order in Structs and Tuples

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `drop-order`, `destructors`, `struct-fields`, `LIFO`

## Problem Statement

Consider this code that tracks drop order:

```rust
struct Noisy(&'static str);

impl Drop for Noisy {
    fn drop(&mut self) {
        println!("dropping {}", self.0);
    }
}

fn main() {
    let a = Noisy("a");
    let b = Noisy("b");
    let c = Noisy("c");

    let t = (Noisy("x"), Noisy("y"), Noisy("z"));

    println!("--- end ---");
}
```

## Question

What is the drop order (the sequence of "dropping ..." messages printed after `--- end ---`)?

## Options

- A) `dropping z, y, x, c, b, a` — tuple fields reversed, then locals reversed
- B) `dropping x, y, z, c, b, a` — tuple fields in order, then locals reversed
- C) `dropping x, y, z, a, b, c` — tuple fields in order, then locals in order
- D) `dropping c, b, a, x, y, z` — locals reversed first, then tuple fields in order
