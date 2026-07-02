# Problem 030: Higher-Ranked Trait Bounds — for<'a> Basics

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `HRTB`, `for-lifetime`, `closures`, `higher-ranked-bounds`

## Problem Statement

Compare these two function signatures:

```rust
fn apply_to_ref_a<'a>(f: fn(&'a str) -> &'a str, s: &'a str) -> &'a str {
    f(s)
}

fn apply_to_ref_b(f: fn(&str) -> &str, s: &str) -> &str {
    f(s)
}

fn apply_to_ref_c(f: for<'a> fn(&'a str) -> &'a str, s: &str) -> &str {
    f(s)
}

fn identity(s: &str) -> &str {
    s
}

fn main() {
    let owned = String::from("hello");
    
    let r1 = apply_to_ref_a(identity, &owned);
    println!("{}", r1);
    
    let r2 = apply_to_ref_b(identity, &owned);
    println!("{}", r2);
    
    let r3 = apply_to_ref_c(identity, &owned);
    println!("{}", r3);
}
```

## Question

Do all three calls compile? What is the relationship between signatures `b` and `c`?

## Options

- A) All three compile; `b` and `c` have identical signatures because Rust desugars `b`'s signature to include `for<'a>`
- B) Only `a` and `b` compile; `c` fails because `for<'a>` is not valid on function pointer types
- C) Only `a` and `c` compile; `b` fails because the lifetime elision is ambiguous
- D) All three compile; `b` and `c` are different — `b` uses a specific lifetime while `c` is universally quantified
