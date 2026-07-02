# Problem 001: Partial Move in Destructuring

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `partial-move`, `destructuring`, `pattern-matching`

## Problem Statement

Consider this code that destructures a tuple containing both `Copy` and non-`Copy` types:

```rust
fn main() {
    let t = (String::from("hello"), 42u32, String::from("world"));

    let (a, b, ref c) = t;

    println!("{}", b);
    println!("{}", c);
    println!("{}", t.1);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: cannot use `t.1` after partial move of `t`
- B) Compiles and prints `42`, `world`, `42`
- C) Compilation error: cannot mix `ref` bindings and move bindings in the same pattern
- D) Compilation error: `b` is moved out of `t`, so `t.1` is invalid
