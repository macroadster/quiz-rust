# Problem 004: Reborrowing Through &mut References

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `reborrowing`, `mutable-references`, `borrow-checker`

## Problem Statement

Examine the following code that passes `&mut` references through multiple functions:

```rust
fn append_bar(s: &mut String) {
    s.push_str("bar");
}

fn append_baz(s: &mut String) {
    append_bar(s);
    s.push_str("baz");
}

fn main() {
    let mut s = String::from("foo");
    append_baz(&mut s);
    println!("{}", s);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: `s` is already mutably borrowed in `append_baz`, cannot pass to `append_bar`
- B) Compiles and prints `foobarbaz`
- C) Compilation error: cannot create second `&mut` reference to `s` inside `append_baz`
- D) Compilation error: `s` is moved into `append_bar`, cannot use `s` after the call
