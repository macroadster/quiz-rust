# Problem 003: Move Through Match Arms with Guard Clauses

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `match`, `move-semantics`, `guard-clause`, `binding-modes`

## Problem Statement

Consider this code that uses `match` with guard clauses and ownership:

```rust
fn main() {
    let opt: Option<String> = Some(String::from("hello"));

    match &opt {
        Some(s) if s.len() > 3 => println!("long: {}", s),
        Some(s) => println!("short: {}", s),
        None => println!("none"),
    }

    println!("{:?}", opt);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: `opt` is moved into the `match` and cannot be used afterward
- B) Compiles and prints `long: hello` then `Some("hello")`
- C) Compilation error: `s` in the guard clause borrows `opt`, preventing later use
- D) Compilation error: cannot match `&Option<String>` with `Some(s)` pattern

## Answer

**Correct: B)**

The code compiles and prints `long: hello` then `Some("hello")`. The critical detail is `match &opt` — we are matching on a *reference* to `opt`, not `opt` itself. When matching `&Option<String>` against the pattern `Some(s)`, Rust's match ergonomics automatically adjust the binding: `s` is bound as `&String`, not `String`. No move occurs at any point. The guard clause `s.len() > 3` works on the `&String` reference (via auto-deref). Since `"hello"` has length 5 > 3, the first arm matches. After the match, `opt` is still fully owned and valid, so `println!("{:?}", opt)` works and prints `Some("hello")`.

## Common LLM Mistakes

1. **Ignoring the `&` in `match &opt`:** LLMs frequently overlook that we're matching on a reference, which triggers match ergonomics. They incorrectly conclude that `opt` is moved into the match.
2. **Misunderstanding match ergonomics:** When matching `&Option<String>` against `Some(s)`, Rust automatically inserts a `ref` binding, making `s: &String`. LLMs may think `s` would be `String` (a move), which would invalidate `opt`.
3. **Confusion about guard clause borrowing:** Some LLMs claim the guard clause creates a borrow that conflicts with later use, but the borrow from the guard only lives for the duration of the match expression.

## Key Concepts

- `match &expr` triggers match ergonomics: patterns bind by reference automatically
- Guard clauses borrow for the duration of the match, not beyond
- Match ergonomics insert implicit `ref` bindings when matching on references
