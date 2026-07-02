# Problem 093: Match Ergonomics and Binding Modes

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `match-ergonomics`, `binding-modes`, `pattern-matching`, `references`

## Problem Statement

A developer uses match ergonomics (RFC 2005) when pattern matching on references:

```rust
fn main() {
    let values: Vec<String> = vec![
        String::from("hello"),
        String::from("world"),
    ];

    for val in &values {
        match val {
            s if s.len() > 3 => println!("long: {}", s),
            s => println!("short: {}", s),
        }
    }

    // Can we still use values?
    println!("count: {}", values.len());
}
```

## Question

What is the output of this program?

## Options

- A) `long: hello`, `long: world`, `count: 2`
- B) Compilation error — `val` is moved in the match, so `values` cannot be used afterward
- C) Compilation error — match patterns on `&String` require explicit `&` or `ref`
- D) `long: hello`, `long: world`, `count: 2` — but `s` is `&String`, not `String`
