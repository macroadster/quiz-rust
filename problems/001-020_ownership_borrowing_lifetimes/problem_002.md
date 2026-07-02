# Problem 002: Move Semantics in Closures with FnOnce

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `closures`, `FnOnce`, `move-semantics`, `capture`

## Problem Statement

Examine this code involving closure capture modes and the `move` keyword:

```rust
fn consume(f: impl FnOnce()) {
    f();
}

fn main() {
    let s = String::from("hello");
    let n = 10u32;

    let closure = move || {
        println!("{}", n);
    };

    closure();
    closure();
    println!("{}", s);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: `closure` cannot be called twice because it is `FnOnce`
- B) Compilation error: `s` has been moved into the closure
- C) Compiles and prints `10`, `10`, `hello`
- D) Compilation error: `move` closures always capture all variables in scope
