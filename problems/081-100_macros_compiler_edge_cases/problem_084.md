# Problem 084: Derive Macro Field Visibility

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `derive-macros`, `procedural-macros`, `Debug`, `visibility`

## Problem Statement

A developer defines a struct with mixed field visibility and derives `Clone`:

```rust
mod inner {
    #[derive(Clone, Debug)]
    pub struct Config {
        pub name: String,
        max_retries: u32,
    }

    impl Config {
        pub fn new() -> Self {
            Config {
                name: String::from("default"),
                max_retries: 3,
            }
        }
    }
}

fn main() {
    let c1 = inner::Config::new();
    let c2 = c1.clone();
    println!("{:?}", c2);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Compilation error — `clone()` cannot access private field `max_retries` from `main`
- B) Prints `Config { name: "default", max_retries: 3 }`
- C) Compilation error — `#[derive(Clone)]` fails because `max_retries` is not `pub`
- D) Prints `Config { name: "default", max_retries: 0 }` — private fields are zero-initialized in clones
