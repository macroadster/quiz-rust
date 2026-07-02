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

## Answer

**Correct: B)**

The output after `--- end ---` is: `dropping x`, `dropping y`, `dropping z`, `dropping c`, `dropping b`, `dropping a`.

Local variables are dropped in **reverse order of declaration** (LIFO): `t` was declared last, so it's dropped first, then `c`, `b`, `a`. Within a tuple (or struct), fields are dropped in **declaration order** (first to last): `t.0` ("x"), then `t.1` ("y"), then `t.2` ("z"). So the full sequence is: `t` drops first, which drops its fields in order (x, y, z), then `c`, then `b`, then `a`.

The crucial distinction: **locals** drop in reverse declaration order, but **fields within a composite** drop in forward declaration order. This is specified in the Rust Reference.

## Common LLM Mistakes

1. **Reversing the tuple field drop order:** LLMs commonly assume fields within tuples/structs are dropped in reverse order, like locals. In fact, struct/tuple fields are dropped in declaration order (forward), while locals are dropped in reverse declaration order.
2. **Dropping locals before `t`:** `t` was the last variable declared, so it must be the first to drop. LLMs sometimes alphabetize or use a different ordering.
3. **Confusing struct field order with local variable order:** The rules are different for these two cases. Locals: reverse. Fields: forward.

## Key Concepts

- Local variables are dropped in reverse order of declaration (LIFO)
- Struct and tuple fields are dropped in forward declaration order
- Drop order is deterministic and specified in the Rust Reference
