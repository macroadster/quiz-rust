# Problem 032: HRTB Limitation — Returning References from Closures

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `HRTB`, `lifetime-bounds`, `closures`, `return-references`

## Problem Statement

Consider this code that tries to accept a closure returning a reference:

```rust
fn find_first<'s, F>(data: &'s [String], predicate: F) -> Option<&'s str>
where
    F: Fn(&'s String) -> bool,
{
    data.iter().find(|s| predicate(s)).map(|s| s.as_str())
}

fn find_first_hrtb<F>(data: &[String], predicate: F) -> Option<&str>
where
    F: for<'a> Fn(&'a String) -> bool,
{
    data.iter().find(|s| predicate(s)).map(|s| s.as_str())
}

fn main() {
    let data = vec!["hello".to_string(), "world".to_string()];
    
    let result1 = find_first(&data, |s| s.starts_with('h'));
    println!("{:?}", result1);
    
    let result2 = find_first_hrtb(&data, |s| s.starts_with('h'));
    println!("{:?}", result2);
}
```

## Question

Do both functions compile and produce the same output?

## Options

- A) Both compile and print `Some("hello")` — they are functionally identical
- B) Only `find_first` compiles; `find_first_hrtb` fails because `for<'a>` conflicts with the lifetime of `data`
- C) Only `find_first_hrtb` compiles; `find_first` has lifetime issues with the closure
- D) Both compile but `find_first_hrtb` is strictly more flexible — it accepts closures that `find_first` rejects

## Answer

**Correct: A)**

Both functions compile and produce identical output: `Some("hello")`.

In `find_first`, the lifetime `'s` ties the data, the predicate's input, and the return value together. The closure `|s| s.starts_with('h')` is inferred to have type `Fn(&'s String) -> bool` for the specific `'s` of the data slice.

In `find_first_hrtb`, the `for<'a>` on the predicate means the closure must work for *any* lifetime. The closure `|s| s.starts_with('h')` naturally satisfies this because `starts_with` works for any lifetime on `&String`.

In this particular use case, both are equivalent because:
1. The closure doesn't capture anything with lifetime-sensitive references
2. The closure body (`starts_with`) is generic over the input lifetime
3. The data's lifetime is properly connected to the return type via elision

The HRTB version is technically more general — it could accept closures in contexts where the concrete lifetime isn't known. But for this specific call pattern, both work identically.

## Common LLM Mistakes

1. **Choosing D (strictly more flexible):** While HRTB is more general in theory, for this specific code both functions accept the same closures. LLMs may over-state the practical difference.
2. **Choosing B (HRTB conflicts):** LLMs sometimes think `for<'a>` creates a conflict with the concrete lifetime of `data`. It doesn't — `for<'a>` on the predicate is independent of the return type's lifetime.
3. **Not understanding lifetime elision in `find_first_hrtb`:** The return type `Option<&str>` gets its lifetime from `&[String]` through standard elision rules, independent of the HRTB on the predicate.

## Key Concepts

- Named lifetime parameters and HRTB can produce equivalent code in many practical scenarios
- HRTB (`for<'a>`) on a closure parameter doesn't affect other lifetime relationships in the function
- Closures that don't capture references typically satisfy both named and HRTB constraints
- The real difference emerges when storing closures or using them in more complex lifetime scenarios
