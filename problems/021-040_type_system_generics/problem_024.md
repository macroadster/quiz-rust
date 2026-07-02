# Problem 024: Orphan Rule with Upstream Trait and Local Type

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `orphan-rules`, `coherence`, `trait-impl`, `foreign-types`

## Problem Statement

A developer has the following code in their own crate:

```rust
use std::fmt;

struct Meters(f64);

impl fmt::Display for Meters {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "{} m", self.0)
    }
}

impl fmt::Display for Vec<Meters> {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        let parts: Vec<String> = self.iter().map(|m| m.to_string()).collect();
        write!(f, "[{}]", parts.join(", "))
    }
}

fn main() {
    let distances = vec![Meters(1.0), Meters(2.5)];
    println!("{}", distances);
}
```

## Question

Does this code compile? If not, what is the error?

## Options

- A) Compiles and prints `[1 m, 2.5 m]`
- B) Fails to compile: cannot implement `Display` for `Vec<Meters>` because neither `Display` nor `Vec` is defined in this crate
- C) Fails to compile: `Vec<Meters>` already has an implementation of `Display`
- D) Compiles but panics at runtime because `Vec` has a conflicting `Display` implementation
