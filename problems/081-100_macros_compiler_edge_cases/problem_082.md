# Problem 082: Push-Down Accumulation in Recursive Macros

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `macro_rules`, `push-down-accumulation`, `tt-munching`, `recursive-macros`

## Problem Statement

A developer uses the push-down accumulation pattern to reverse a list of tokens at compile time:

```rust
macro_rules! reverse {
    ([] $($reversed:tt)*) => {
        stringify!($($reversed)*)
    };
    ([$first:tt $($rest:tt)*] $($reversed:tt)*) => {
        reverse!([$($rest)*] $first $($reversed)*)
    };
}

fn main() {
    let s = reverse!([a b c]);
    println!("{}", s);
}
```

## Question

What is the output of this program?

## Options

- A) `"a b c"`
- B) `"c b a"`
- C) `"c  b  a"` (with extra spaces)
- D) Compilation error — macro recursion limit exceeded

## Answer

**Correct: B)**

The macro uses push-down accumulation to reverse the tokens. Here's the expansion trace:

1. `reverse!([a b c])` → matches second arm: `reverse!([b c] a)`
2. `reverse!([b c] a)` → matches second arm: `reverse!([c] b a)`
3. `reverse!([c] b a)` → matches second arm: `reverse!([] c b a)`
4. `reverse!([] c b a)` → matches first arm: `stringify!(c b a)`

`stringify!` produces the string `"c b a"` with single spaces between tokens (it normalizes whitespace). The output is `c b a`.

## Common LLM Mistakes

1. **Getting the accumulation direction wrong:** LLMs sometimes confuse whether `$first` is prepended or appended to the accumulator. Here, `$first` is placed *before* `$($reversed)*`, so each new token goes to the front, producing a reversal.
2. **Worrying about `stringify!` spacing:** LLMs may claim extra spaces or no spaces. `stringify!` normalizes token spacing to single spaces between tokens.
3. **Thinking the recursion limit is hit:** The default recursion limit is 128, and this only recurses 4 times. LLMs sometimes overestimate recursion depth or confuse token count with recursion depth.

## Key Concepts

- Push-down accumulation passes intermediate results as macro arguments in recursive calls
- `tt` (token tree) fragments match any single token or delimited group
- `stringify!` normalizes whitespace to single spaces between tokens
- Macro recursion depth equals the number of elements being processed, not a combinatorial explosion
