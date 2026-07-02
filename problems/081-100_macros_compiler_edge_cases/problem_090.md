# Problem 090: Method Resolution with Autoref and Trait Implementations

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `method-resolution`, `autoref`, `autoderef`, `trait-dispatch`

## Problem Statement

A developer implements a trait for both a type and its reference:

```rust
trait Greet {
    fn greet(&self) -> &'static str;
}

struct Person;

impl Greet for Person {
    fn greet(&self) -> &'static str {
        "Hello from Person"
    }
}

impl Greet for &Person {
    fn greet(&self) -> &'static str {
        "Hello from &Person"
    }
}

fn main() {
    let p = Person;
    let r = &p;
    println!("{}", p.greet());
    println!("{}", r.greet());
}
```

## Question

What is the output of this program?

## Options

- A) `Hello from Person` and `Hello from &Person`
- B) `Hello from Person` and `Hello from Person`
- C) `Hello from &Person` and `Hello from &Person`
- D) Compilation error — ambiguous method call for `r.greet()`
