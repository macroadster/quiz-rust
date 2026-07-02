# Problem 021: Generic Method in Trait Object

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `object-safety`, `trait-objects`, `generic-methods`

## Problem Statement

Consider the following code that attempts to use a trait with a generic method as a trait object:

```rust
trait Processor {
    fn process<T: std::fmt::Display>(&self, value: T) -> String;
}

struct Upper;

impl Processor for Upper {
    fn process<T: std::fmt::Display>(&self, value: T) -> String {
        format!("{}", value).to_uppercase()
    }
}

fn apply(p: &dyn Processor) {
    println!("{}", p.process(42));
}

fn main() {
    let u = Upper;
    apply(&u);
}
```

## Question

Does this code compile? If not, what is the error?

## Options

- A) Compiles and prints `42`
- B) Compiles and prints `42` in uppercase (but integers don't change, so still `42`)
- C) Fails to compile: the trait `Processor` cannot be made into an object because method `process` has generic type parameters
- D) Fails to compile: `&Upper` cannot be coerced to `&dyn Processor`
