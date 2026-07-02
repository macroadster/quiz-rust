# Problem 013: Covariance of Shared References

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `variance`, `covariance`, `lifetimes`, `subtyping`

## Problem Statement

Consider this code that exploits lifetime subtyping through shared references:

```rust
fn choose<'a>(cond: bool, x: &'a str, y: &'a str) -> &'a str {
    if cond { x } else { y }
}

fn main() {
    let s1 = String::from("long-lived");
    let result;
    {
        let s2 = String::from("short-lived");
        result = choose(true, &s1, &s2);
    }
    println!("{}", result);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `long-lived` because the compiler knows `cond` is `true` and only `s1` is returned
- B) Compilation error: `s2` does not live long enough — `result` must be valid outside the inner scope
- C) Compiles and prints `long-lived` because `&'a str` is covariant, so the longer lifetime can be shortened
- D) Compilation error: `s1` and `s2` have different lifetimes, cannot unify into `'a`
