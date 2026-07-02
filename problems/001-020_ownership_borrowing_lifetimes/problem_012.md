# Problem 012: Lifetime Elision with Struct Impls Returning Inner References

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `lifetime-elision`, `struct-references`, `hidden-lifetimes`

## Problem Statement

Consider the following code where a method's elided lifetimes interact with a struct containing a reference:

```rust
struct Parser<'a> {
    input: &'a str,
    pos: usize,
}

impl<'a> Parser<'a> {
    fn remaining(&self) -> &str {
        &self.input[self.pos..]
    }
}

fn main() {
    let text = String::from("hello world");
    let parser = Parser { input: &text, pos: 6 };
    let r = parser.remaining();
    drop(parser);
    println!("{}", r);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `world`
- B) Compilation error: cannot use `r` after dropping `parser` because `r`'s lifetime is tied to `parser`
- C) Compilation error: `remaining()` cannot return `&str` without explicit lifetime annotations
- D) Compiles and prints `hello world`

## Answer

**Correct: B)**

The code does **not** compile. The method `remaining(&self) -> &str` has its output lifetime elided to the lifetime of `&self` (elision rule 3). The desugared signature is `fn remaining(&'b self) -> &'b str` — the returned reference is tied to the borrow of `parser`, NOT to the underlying `'a` lifetime of the string data. So when we call `drop(parser)`, the `parser` is consumed and its borrow ends. But `r` is tied to the lifetime of `&self` (the borrow of `parser`), so `r` is considered dangling after `parser` is dropped.

If the signature were explicitly `fn remaining(&self) -> &'a str`, the returned reference would be tied to the *input data*'s lifetime (`text`), and the code would compile. But elision doesn't know about the struct's `'a` — it mechanically ties the output to `&self`.

## Common LLM Mistakes

1. **Assuming elision connects the output to the struct's inner lifetime `'a`:** Elision rule 3 ties the output to `&self`'s lifetime, not to any lifetime parameter on the struct. This is a common trap when structs contain references.
2. **Thinking it compiles because the underlying data (`text`) is still alive:** The data is alive, but the *type system* ties `r` to `parser`'s borrow, not to `text`. The borrow checker works with static type information, not runtime reachability.
3. **Missing that explicit lifetime annotation would fix this:** Annotating `-> &'a str` would correctly tie the output to the input data's lifetime, making the code compile.

## Key Concepts

- Lifetime elision rule 3 ties output to `&self`, not to the struct's lifetime parameters
- Elision can produce overly restrictive lifetimes when structs contain references
- Explicit lifetime annotations can be more permissive than elided ones
