# Problem 025: Blanket Impl Conflict

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `coherence`, `blanket-impls`, `trait-overlap`, `conflicting-implementations`

## Problem Statement

Consider the following code with a blanket implementation and a specific implementation:

```rust
trait Describe {
    fn describe(&self) -> String;
}

impl<T: std::fmt::Display> Describe for T {
    fn describe(&self) -> String {
        format!("Display: {}", self)
    }
}

impl Describe for Vec<i32> {
    fn describe(&self) -> String {
        format!("Vec with {} elements", self.len())
    }
}

fn main() {
    let v = vec![1, 2, 3];
    println!("{}", v.describe());
}
```

## Question

Does this code compile? If not, what is the error?

## Options

- A) Compiles and prints `Vec with 3 elements` because specific impls take priority over blanket impls
- B) Compiles and prints `Display: [1, 2, 3]` because blanket impls take priority
- C) Fails to compile: conflicting implementations of `Describe` for `Vec<i32>`
- D) Fails to compile: `Vec<i32>` does not implement `Display`
