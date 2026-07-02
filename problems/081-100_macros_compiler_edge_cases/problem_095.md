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

## Answer

**Correct: B)**

`classify` compiles fine because the compiler can verify that the three range patterns (`0`, `1..=127`, `128..=255`) cover all 256 possible `u8` values. Rust's exhaustiveness checker understands integer range patterns and can determine completeness.

`classify_with_guard` fails because match guards (`if` conditions) are **never** considered by the exhaustiveness checker. Even though the guards `x == 0`, `x <= 127`, and `x <= 255` logically cover all `u8` values, the compiler treats each arm as potentially non-matching. From the compiler's perspective, all three guards could evaluate to `false`, leaving no matching arm.

To fix `classify_with_guard`, you would need a wildcard arm `_ => "positive large"` or remove the guard from the last arm.

## Common LLM Mistakes

1. **Thinking the compiler analyzes guard conditions for exhaustiveness:** The compiler's exhaustiveness checker completely ignores `if` guards. It treats guarded arms as if they might not match, regardless of how trivially complete the guards appear.
2. **Assuming both functions behave the same:** The difference between pattern ranges and match guards is fundamental to how the compiler verifies exhaustiveness.
3. **Thinking integer ranges require a wildcard:** The compiler *can* verify exhaustiveness of integer range patterns. `0`, `1..=127`, `128..=255` covers all 256 `u8` values without needing `_`.

## Key Concepts

- Match guards (`if` conditions) are never considered by Rust's exhaustiveness checker
- Integer range patterns *are* considered — the compiler can verify they cover the full range
- The exhaustiveness checker is conservative with guards — it assumes any guard may fail
- Adding a wildcard `_` arm resolves exhaustiveness issues with guarded arms
