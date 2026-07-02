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

## Answer

**Correct: B)**

The code compiles and prints `first: 1` then `second: 2`. Under NLL (Non-Lexical Lifetimes), borrows end at their **last point of use**, not at the end of the lexical scope. The immutable borrow `first` is last used in `println!("first: {}", first)`. After that line, the borrow on `v` through `first` is over. By the time `v.push(4)` executes, there are no active borrows, so the mutable borrow for `push` is valid. A new immutable borrow `second` is then created from the (potentially reallocated) vector. Under the pre-NLL borrow checker, this code *would* have failed because `first` would have been live until the end of `main`. NLL recognizes that `first` is dead before `push` is called.

## Common LLM Mistakes

1. **Applying pre-NLL (lexical lifetime) rules:** LLMs trained on older Rust materials may apply the old rule that borrows last until the end of their lexical scope, incorrectly rejecting this code.
2. **Confusing dangling reference concerns with borrow-check rules:** The concern about `push` invalidating `first` is real *if* `first` were still alive — but it isn't. NLL ensures the borrow ends before `push`.
3. **Choosing the "runtime panic" option:** There is no runtime check for dangling references in safe Rust. The borrow checker prevents this at compile time. If it compiles, there's no panic from invalidation.

## Key Concepts

- NLL ends borrows at the last point of use, not at end of scope
- The borrow checker ensures references are never dangling — if it compiles, it's safe
- `Vec::push` requires `&mut self`, which is only allowed when no other borrows are active
