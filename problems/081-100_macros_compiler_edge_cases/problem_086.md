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
