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

## Answer

**Correct: C)**

This code compiles and works correctly. Let's trace through each call:

1. `println!("{}", mv)` — calls `Display` for `MyVec`, which is directly implemented. Prints `[a, b, c]`.
2. `println!("{}", mv.summary())` — `MyVec` doesn't implement `Summarize`, but through auto-deref, `mv` is dereferenced to `&Vec<String>`, which does implement `Summarize`. This prints `3 items`.
3. `println!("len = {}", mv.len())` — `MyVec` doesn't have `len()`, but through `Deref`, it accesses `Vec<String>::len()`. Prints `len = 3`.

Both options A and C describe the same output, but C correctly identifies that `summary()` works through auto-deref coercion rather than through a direct implementation on `MyVec`. This is an important distinction: `MyVec` itself does *not* implement `Summarize`.

Importantly, `Summarize` is a *local* trait, so implementing it for `Vec<String>` (a foreign type) is perfectly valid under the orphan rules — you can always implement your own traits for any type.

## Common LLM Mistakes

1. **Choosing B (orphan violation):** LLMs often confuse which direction the orphan rule works. You can always implement a *local* trait for a foreign type. The restriction is on implementing a *foreign* trait for a foreign type.
2. **Choosing D (deref doesn't help):** LLMs sometimes claim deref coercion doesn't apply for trait methods. In fact, Rust's method resolution includes auto-deref steps, so `mv.summary()` will deref `MyVec` to `Vec<String>` and find the `Summarize` impl.
3. **Not distinguishing A from C:** Both produce the same output, but C correctly explains the *mechanism*. The distinction matters for understanding — `MyVec` doesn't implement `Summarize`, the deref to `Vec<String>` does.

## Key Concepts

- The orphan rule allows implementing local traits for any type (foreign or local)
- The restriction is: foreign trait + foreign type = not allowed
- `Deref` enables transparent access to the inner type's methods through auto-deref in method calls
- Auto-deref during method resolution will find trait implementations on the target type
