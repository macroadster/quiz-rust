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
