# Problem 010: Lifetime Elision with Multiple Input References

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `lifetime-elision`, `elision-rules`, `function-signatures`

## Problem Statement

Consider these two function signatures — are they equivalent?

```rust
// Version A: explicit lifetimes
fn first_or_second<'a, 'b>(x: &'a str, y: &'b str) -> &'a str {
    x
}

// Version B: elided lifetimes
fn first_or_default(x: &str, y: &str) -> &str {
    x
}

fn main() {
    let result;
    {
        let a = String::from("hello");
        let b = String::from("world");
        result = first_or_second(&a, &b);
    }
    // println!("{}", result);  // Not used yet
}
```

## Question

Which statement is correct about the function `first_or_default` (Version B)?

## Options

- A) It compiles with the same semantics as Version A — elision assigns the first input lifetime to the output
- B) It fails to compile because the lifetime elision rules cannot determine the output lifetime when there are two input references
- C) It compiles, but the output lifetime is the shorter of the two input lifetimes
- D) It compiles, and the output lifetime is `'static`
