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

## Answer

**Correct: C)**

The trait `Processor` is not object-safe because it contains a method `process` with a generic type parameter `T`. Trait objects work through vtables, and each vtable entry must correspond to a single concrete function pointer. A generic method like `process<T>` would need infinitely many entries in the vtable — one for every possible type `T` — which is impossible. The compiler therefore refuses to create `dyn Processor` at all.

The error occurs at the function signature `fn apply(p: &dyn Processor)` where Rust tries to form the trait object type `dyn Processor`. The error message is: "the trait `Processor` cannot be made into an object because method `process` has generic type parameters."

Note that option D is wrong because the coercion itself isn't the problem — the problem is that `dyn Processor` cannot exist as a type at all. If the trait were object-safe, `&Upper` would coerce to `&dyn Processor` without issue.

## Common LLM Mistakes

1. **Confusing object safety with trait bounds:** LLMs sometimes think adding `: Display` makes a method non-object-safe, when it's actually the generic type *parameter* `T` that's the issue. A method like `fn process(&self, value: &dyn Display)` would be perfectly fine.
2. **Thinking it compiles:** LLMs may not track object safety rules and assume the code works, predicting output instead of a compilation error.
3. **Choosing D over C:** LLMs sometimes blame the coercion site rather than recognizing the trait itself is fundamentally not object-safe.

## Key Concepts

- Object safety requires that all methods in a trait can be dispatched through a vtable
- Generic type parameters on methods make a trait non-object-safe because the vtable cannot enumerate all possible monomorphizations
- The fix would be to use `&dyn Display` instead of a generic parameter, or to use `where Self: Sized` to exclude the method from the trait object
