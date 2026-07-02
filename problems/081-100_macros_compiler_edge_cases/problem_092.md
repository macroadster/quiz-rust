# Problem 092: Method Call on Owned vs Borrowed Self

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `method-resolution`, `self-type`, `move-semantics`, `autoref`

## Problem Statement

A developer defines a type with methods that take `self` by different receiver types:

```rust
#[derive(Debug)]
struct Token(String);

impl Token {
    fn consume(self) -> String {
        self.0
    }

    fn inspect(&self) -> &str {
        &self.0
    }
}

fn main() {
    let t = Token(String::from("abc"));
    let r: &Token = &t;

    // Call through a reference
    let s = r.consume();
    println!("{}", s);
}
```

## Question

What happens when this program is compiled?

## Options

- A) Prints `abc` — the reference is automatically dereferenced and the value is moved
- B) Compilation error — cannot move out of `*r` which is behind a shared reference
- C) Compilation error — `consume()` is not accessible through a reference
- D) Prints `abc` — `r.consume()` implicitly clones the `Token`
