# Problem 019: Drop Order Interaction with Borrows

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `drop-order`, `borrows`, `NLL`, `destructor`, `borrow-interaction`

## Problem Statement

Consider this code where a custom `Drop` implementation interacts with borrows:

```rust
struct LogOnDrop<'a> {
    label: &'a str,
}

impl<'a> Drop for LogOnDrop<'a> {
    fn drop(&mut self) {
        println!("dropping: {}", self.label);
    }
}

fn main() {
    let s = String::from("hello");
    let logger = LogOnDrop { label: &s };
    drop(s);
    println!("after drop");
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `dropping: hello` then `after drop`
- B) Compilation error: cannot move `s` because it is borrowed by `logger`
- C) Compiles and prints `after drop` then `dropping: hello`
- D) Compiles and prints `after drop` only — `logger` doesn't have data to print

## Answer

**Correct: B)**

The code does not compile. `logger` holds a reference `&s`, which borrows `s`. When we call `drop(s)`, we attempt to move `s` (passing it by value to `std::mem::drop`). But `s` is still borrowed by `logger`, which is alive until the end of `main`. The borrow checker sees that `logger`'s `label` field references `s`, and `logger` has a `Drop` implementation — this is critical. Because `LogOnDrop` implements `Drop`, the compiler must keep the borrow alive until `logger` is dropped (at end of scope), since the destructor could access the borrowed data. Therefore, `s` cannot be moved while `logger` exists.

If `LogOnDrop` did **not** implement `Drop`, NLL might allow this in some cases because the compiler could see that `logger` is never used after `drop(s)`. But with a `Drop` impl, the destructor is an implicit "use" of all the struct's fields at the end of scope.

## Common LLM Mistakes

1. **Ignoring the interaction between `Drop` and borrows:** LLMs often think NLL allows this because `logger` isn't explicitly used after `drop(s)`. But `Drop::drop` is an implicit use at end of scope — it accesses `self.label`, keeping the borrow alive.
2. **Claiming it compiles and reordering output:** Some LLMs think `drop(s)` works and `logger`'s destructor runs with invalid data. In safe Rust, the borrow checker prevents this entirely.
3. **Forgetting that `drop(s)` moves `s`:** `std::mem::drop` takes ownership by value. It doesn't just "mark" `s` as dropped — it moves `s` into the function, which requires sole ownership.

## Key Concepts

- `Drop` implementations create an implicit use of all fields at end of scope
- The borrow checker treats destructors as reads of borrowed data
- Types with `Drop` impls extend the liveness of borrows to the end of the variable's scope
