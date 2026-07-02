# Problem 074: Temporary Drop Timing in Match Guards

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `drop-order`, `temporaries`, `scope`, `match-expressions`

## Problem Statement

A developer creates temporaries inside a `let` binding with a complex expression.

```rust
struct Noisy(&'static str);

impl Drop for Noisy {
    fn drop(&mut self) {
        print!("{} ", self.0);
    }
}

impl Noisy {
    fn value(&self) -> i32 {
        match self.0 {
            "first" => 1,
            "second" => 2,
            _ => 0,
        }
    }
}

fn main() {
    let val = Noisy("first").value() + Noisy("second").value();
    print!("sum={} ", val);
}
```

## Question

What is the output of this program?

## Options

- A) `first second sum=3`
- B) `second first sum=3`
- C) `sum=3 second first`
- D) `sum=3 first second`

## Answer

**Correct: B)**

The temporaries `Noisy("first")` and `Noisy("second")` are created during the evaluation of the `let` expression. In Rust, temporaries created in a statement are dropped at the **end of the statement**, but within a complex expression, the sub-expressions are evaluated left to right. However, temporaries are dropped in **reverse** order of creation at the end of the statement.

Here's the sequence:
1. `Noisy("first")` is created (temporary 1)
2. `.value()` is called on it → returns `1`
3. `Noisy("second")` is created (temporary 2)
4. `.value()` is called on it → returns `2`
5. `val = 3` is computed
6. Temporaries are dropped in reverse creation order: `Noisy("second")` drops first → prints `second`, then `Noisy("first")` drops → prints `first`
7. `print!("sum={} ", val)` → prints `sum=3`

Output: `second first sum=3`

## Common LLM Mistakes

1. **Claiming temporaries drop after the print statement:** The temporaries are part of the `let val = ...;` statement and are dropped at the **semicolon** of that statement, before the next statement executes.
2. **Claiming forward drop order:** Temporaries drop in reverse creation order (like a stack — last created, first dropped).
3. **Claiming temporaries live until end of block:** Temporaries in `let` value expressions are dropped at end of statement, not end of block. They would only live until end of block if bound to a `let` binding with a reference.

## Key Concepts

- Temporaries in `let` value expressions are dropped at the end of the **statement** (the semicolon)
- Multiple temporaries in one expression drop in **reverse creation order**
- This happens before subsequent statements execute
- Temporaries bound by `let ref` or `let ... = &temp` have their lifetimes extended to the block scope
