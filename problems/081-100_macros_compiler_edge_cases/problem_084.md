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

## Answer

**Correct: B)**

The `#[derive(Clone)]` macro generates an `impl Clone for Config` block *inside the same module* where the struct is defined (`inner`). Since the generated code is in the same module, it has full access to all fields, including private ones. The generated `clone()` method copies all fields correctly.

When `main()` calls `c1.clone()`, it's calling the public method from the `Clone` trait — the caller doesn't need access to private fields because the actual field access happens inside the generated impl, which is in the struct's own module.

Similarly, `Debug` can print private fields because its generated impl is also in the same module.

## Common LLM Mistakes

1. **Thinking derive macros respect the caller's visibility:** LLMs often reason that since `main()` can't access `max_retries`, `clone()` shouldn't work. But the derived impl is generated *in the defining module*, not at the call site.
2. **Confusing field visibility with method visibility:** The `clone()` method is public (from the `Clone` trait). The *implementation* of that method accesses private fields, but it's allowed because it's in the same module.
3. **Thinking `#[derive]` itself checks visibility:** The derive macro generates code in the same module as the struct definition, so field visibility is never an issue for derived implementations.

## Key Concepts

- `#[derive]` macros generate impls in the same module as the struct definition
- Private fields are accessible to code in the same module, including derived impls
- The caller of `clone()` doesn't need field access — the method body handles it
- This applies to all derive macros: `Clone`, `Debug`, `PartialEq`, `Hash`, etc.
