# Problem 026: Newtype Pattern and Deref Coherence

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `orphan-rules`, `newtype-pattern`, `coherence`, `Deref`

## Problem Statement

A developer uses the newtype pattern to implement a foreign trait on a foreign type, then implements `Deref` to make the wrapper transparent:

```rust
use std::ops::Deref;
use std::fmt;

struct MyVec(Vec<String>);

impl Deref for MyVec {
    type Target = Vec<String>;
    fn deref(&self) -> &Vec<String> {
        &self.0
    }
}

impl fmt::Display for MyVec {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "[{}]", self.0.join(", "))
    }
}

trait Summarize {
    fn summary(&self) -> String;
}

impl Summarize for Vec<String> {
    fn summary(&self) -> String {
        format!("{} items", self.len())
    }
}

fn main() {
    let mv = MyVec(vec!["a".into(), "b".into(), "c".into()]);
    println!("{}", mv);
    println!("{}", mv.summary());
    println!("len = {}", mv.len());
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints: `[a, b, c]`, `3 items`, `len = 3`
- B) Fails to compile: cannot implement `Summarize` for `Vec<String>` (orphan rule)
- C) Compiles and prints: `[a, b, c]`, `3 items`, `len = 3` — but `mv.summary()` uses auto-deref
- D) Fails to compile: `MyVec` does not implement `Summarize`, and deref coercion doesn't apply for trait method resolution
