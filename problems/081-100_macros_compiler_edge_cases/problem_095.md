# Problem 095: Exhaustiveness Checking with Integer Ranges and Guards

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `pattern-matching`, `exhaustiveness`, `match-guards`, `integer-ranges`

## Problem Statement

A developer writes a match on a `u8` value using range patterns and guards:

```rust
fn classify(n: u8) -> &'static str {
    match n {
        0 => "zero",
        1..=127 => "positive small",
        128..=255 => "positive large",
    }
}

fn classify_with_guard(n: u8) -> &'static str {
    match n {
        x if x == 0 => "zero",
        x if x <= 127 => "positive small",
        x if x <= 255 => "positive large",
    }
}

fn main() {
    println!("{}", classify(42));
    println!("{}", classify_with_guard(42));
}
```

## Question

What happens when this program is compiled?

## Options

- A) Both functions compile and print `positive small` twice
- B) `classify` compiles but `classify_with_guard` fails — match guards are not considered for exhaustiveness checking
- C) Both fail — the ranges don't cover all `u8` values
- D) `classify_with_guard` compiles but `classify` fails — range patterns require explicit wildcards
