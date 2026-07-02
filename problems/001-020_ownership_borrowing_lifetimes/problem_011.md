# Problem 011: Lifetime Elision in Impl Methods

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `lifetime-elision`, `methods`, `self-parameter`, `impl`

## Problem Statement

Consider this code with a method that returns a reference:

```rust
struct Config {
    name: String,
    default_value: String,
}

impl Config {
    fn get_or_default(&self, input: &str) -> &str {
        if input.is_empty() {
            &self.default_value
        } else {
            input
        }
    }
}

fn main() {
    let config = Config {
        name: String::from("app"),
        default_value: String::from("default"),
    };

    let result;
    {
        let input = String::from("hello");
        result = config.get_or_default(&input);
    }
    println!("{}", result);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `hello`
- B) Compiles and prints `default`
- C) Compilation error: `input` does not live long enough — `result` requires it to live as long as `config`
- D) Compilation error: the method body cannot return `input` because the elided output lifetime is tied to `&self`
