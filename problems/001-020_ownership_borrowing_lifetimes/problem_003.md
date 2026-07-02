# Problem 003: Move Through Match Arms with Guard Clauses

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `match`, `move-semantics`, `guard-clause`, `binding-modes`

## Problem Statement

Consider this code that uses `match` with guard clauses and ownership:

```rust
fn main() {
    let opt: Option<String> = Some(String::from("hello"));

    match &opt {
        Some(s) if s.len() > 3 => println!("long: {}", s),
        Some(s) => println!("short: {}", s),
        None => println!("none"),
    }

    println!("{:?}", opt);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: `opt` is moved into the `match` and cannot be used afterward
- B) Compiles and prints `long: hello` then `Some("hello")`
- C) Compilation error: `s` in the guard clause borrows `opt`, preventing later use
- D) Compilation error: cannot match `&Option<String>` with `Some(s)` pattern
