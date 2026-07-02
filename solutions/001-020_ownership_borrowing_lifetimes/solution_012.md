# Problem 012: Lifetime Elision with Struct Impls Returning Inner References — Solution

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
