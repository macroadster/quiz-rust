# Problem 014: Invariance of &mut T in Lifetimes

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `variance`, `invariance`, `mutable-references`, `lifetimes`

## Problem Statement

Consider this code that attempts to use `&mut` references with different lifetimes:

```rust
fn extend_ref<'a>(slice: &mut &'a str, s: &'a str) {
    *slice = s;
}

fn main() {
    let mut r: &str = "initial";
    {
        let s = String::from("temporary");
        extend_ref(&mut r, &s);
    }
    println!("{}", r);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `temporary`
- B) Compilation error: `s` does not live long enough — `r` outlives the inner block
- C) Compiles and prints `initial` because the mutation is scoped to the inner block
- D) Compilation error: cannot borrow `r` as mutable because `&mut &str` is not a valid type
