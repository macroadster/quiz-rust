# Problem 008: Two-Phase Borrowing

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `two-phase-borrowing`, `NLL`, `method-calls`, `autoref`

## Problem Statement

Consider this code that seems to create conflicting borrows in a single expression:

```rust
fn main() {
    let mut v = vec![1, 2, 3];
    v.push(v.len());
    println!("{:?}", v);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `[1, 2, 3, 3]`
- B) Compilation error: cannot borrow `v` as immutable (`v.len()`) because it is also borrowed as mutable (`v.push()`)
- C) Compiles and prints `[1, 2, 3, 4]`
- D) Compilation error: cannot borrow `v` as mutable (`v.push()`) because it is also borrowed as immutable (`v.len()`)
