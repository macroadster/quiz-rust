# Problem 012: Lifetime Elision with Struct Impls Returning Inner References

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `lifetime-elision`, `struct-references`, `hidden-lifetimes`

## Problem Statement

Consider the following code where a method's elided lifetimes interact with a struct containing a reference:

```rust
struct Parser<'a> {
    input: &'a str,
    pos: usize,
}

impl<'a> Parser<'a> {
    fn remaining(&self) -> &str {
        &self.input[self.pos..]
    }
}

fn main() {
    let text = String::from("hello world");
    let parser = Parser { input: &text, pos: 6 };
    let r = parser.remaining();
    drop(parser);
    println!("{}", r);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `world`
- B) Compilation error: cannot use `r` after dropping `parser` because `r`'s lifetime is tied to `parser`
- C) Compilation error: `remaining()` cannot return `&str` without explicit lifetime annotations
- D) Compiles and prints `hello world`
