# Problem 017: Why Lifetime-Based Self-Referential Structs Don't Work

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `self-referential`, `lifetimes`, `struct-definition`, `borrow-checker`

## Problem Statement

A developer tries to create a self-referential struct using safe Rust references:

```rust
struct SelfRef<'a> {
    data: String,
    slice: &'a str,
}

impl<'a> SelfRef<'a> {
    fn new(data: String) -> SelfRef<'a> {
        SelfRef {
            slice: &data,
            data,
        }
    }
}

fn main() {
    let s = SelfRef::new(String::from("hello"));
    println!("{}", s.slice);
}
```

## Question

Does this code compile?

## Options

- A) Yes — the struct stores both the data and a slice into it
- B) Compilation error: `data` is moved into the struct field, so `&data` is invalid
- C) Compilation error: `data` does not live long enough — it is dropped at the end of `new()`, but the reference must live for `'a`
- D) Compilation error: cannot have a struct field that references another field in the same struct
