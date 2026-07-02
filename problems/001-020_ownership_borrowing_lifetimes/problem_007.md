# Problem 007: NLL — Borrow Ends at Last Use

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `NLL`, `non-lexical-lifetimes`, `borrow-scope`

## Problem Statement

Consider this code that relies on Non-Lexical Lifetimes (NLL):

```rust
fn main() {
    let mut v = vec![1, 2, 3];

    let first = &v[0];
    println!("first: {}", first);

    v.push(4);

    let second = &v[1];
    println!("second: {}", second);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: `v` is immutably borrowed by `first`, cannot call `v.push(4)`
- B) Compiles and prints `first: 1` then `second: 2`
- C) Compilation error: `first` may be invalidated by `v.push(4)`, creating a dangling reference
- D) Runtime panic: `push` may reallocate, invalidating `first`
