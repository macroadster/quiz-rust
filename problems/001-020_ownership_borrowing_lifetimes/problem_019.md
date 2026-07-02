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
