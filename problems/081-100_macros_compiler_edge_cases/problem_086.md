# Problem 086: Token Pasting and Identifier Construction in macro_rules!

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `macro_rules`, `concat_idents`, `identifier-construction`, `token-streams`

## Problem Statement

A developer tries to construct new identifiers by concatenating tokens inside a `macro_rules!` macro:

```rust
macro_rules! make_fn {
    ($prefix:ident, $suffix:ident) => {
        fn $prefix_$suffix() -> &'static str {
            stringify!($prefix_$suffix)
        }
    };
}

make_fn!(get, name);

fn main() {
    println!("{}", get_name());
}
```

## Question

What happens when this program is compiled?

## Options

- A) Prints `get_name`
- B) Compilation error — `$prefix_$suffix` is not valid identifier construction; `_` is part of the ident `$prefix_`
- C) Compilation error — `macro_rules!` cannot create new identifiers by concatenation
- D) Prints `get _ name` with spaces

## Answer

**Correct: B)**

In `macro_rules!`, `$prefix_$suffix` does **not** concatenate two identifiers with an underscore. The parser treats `$prefix_` as a single metavariable name (since `_` is a valid identifier character), and then `$suffix` as a separate metavariable. Since the macro declared `$prefix` and `$suffix` but not `$prefix_`, the compiler reports that `$prefix_` is an unrecognized metavariable.

`macro_rules!` has no built-in mechanism for constructing new identifiers by concatenation. This is a fundamental limitation — `macro_rules!` operates on whole token trees and cannot synthesize new tokens by combining parts of existing ones. To achieve identifier concatenation, you need either:

1. The `paste` crate (which provides `paste!` / `[<$prefix _ $suffix>]` syntax)
2. A procedural macro that can manipulate `TokenStream` directly

## Common LLM Mistakes

1. **Assuming `$prefix_$suffix` concatenates with underscore:** LLMs often treat this like C's `##` token pasting. Rust's `macro_rules!` has no such feature. The `_` is parsed as part of the metavariable name.
2. **Thinking this produces a valid function `get_name`:** Since `$prefix_` is not a declared metavariable, this is simply a parse error in the macro definition.
3. **Suggesting `concat_idents!` as a solution:** While `concat_idents!` exists as an unstable nightly feature, it has severe limitations and cannot be used in item position on stable Rust. The `paste` crate is the practical solution.

## Key Concepts

- `macro_rules!` cannot construct new identifiers by token concatenation
- `_` is a valid identifier character, so `$prefix_` is parsed as one metavariable name
- The `paste` crate provides `[<$a _ $b>]` syntax for identifier concatenation
- Procedural macros can construct arbitrary identifiers via `TokenStream` manipulation
