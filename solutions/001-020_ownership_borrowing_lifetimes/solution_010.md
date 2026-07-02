# Problem 010: Lifetime Elision with Multiple Input References — Solution

## Answer

**Correct: B)**

The function `first_or_default` fails to compile. Rust's lifetime elision rules are:
1. Each input reference gets its own lifetime parameter.
2. If there is exactly **one** input lifetime, it is assigned to all output references.
3. If one of the inputs is `&self` or `&mut self`, the lifetime of `self` is assigned to all output references.

For `first_or_default(x: &str, y: &str) -> &str`, rule 1 assigns distinct lifetimes `'a` and `'b` to `x` and `y`. Rule 2 does not apply (there are two input lifetimes, not one). Rule 3 does not apply (no `self` parameter). Therefore, the compiler cannot determine the output lifetime, and the function fails to compile with an error about missing lifetime specifier on the return type.

## Common LLM Mistakes

1. **Assuming elision picks the first parameter's lifetime:** There is no such rule. LLMs sometimes fabricate a "first parameter wins" elision rule. Elision only assigns when there's exactly one input lifetime.
2. **Claiming the compiler infers the shorter lifetime:** The compiler does not infer "the shorter of two lifetimes" through elision. That would require explicit annotation like `fn f<'a>(x: &'a str, y: &'a str) -> &'a str`.
3. **Confusing elision with inference:** Lifetime elision is a syntactic sugar based on fixed rules, not semantic inference. The compiler does not look at the function body to figure out the correct lifetime.

## Key Concepts

- Lifetime elision has exactly three rules, applied in order
- Elision fails when there are multiple input lifetimes and no `self` parameter
- Elision is syntactic, not semantic — it doesn't examine the function body
