# Problem 009: NLL and Conditional Borrow Usage

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `NLL`, `control-flow`, `conditional-borrows`, `borrow-scope`

## Problem Statement

Consider this code where a borrow's liveness depends on control flow:

```rust
fn main() {
    let mut data = String::from("hello");

    let r: &str;
    let do_borrow = true;

    if do_borrow {
        r = &data;
        println!("{}", r);
    }

    data.push_str(" world");
    println!("{}", data);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `hello` then `hello world`
- B) Compilation error: `data` is immutably borrowed by `r`, cannot mutably borrow for `push_str`
- C) Compilation error: `r` might not be initialized in all branches
- D) Compilation error: `r` is still live after the `if` block, conflicts with `push_str`
