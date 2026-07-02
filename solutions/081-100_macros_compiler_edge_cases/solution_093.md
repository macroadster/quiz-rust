# Problem 093: Match Ergonomics and Binding Modes — Solution

## Answer

**Correct: D)**

This compiles and outputs `long: hello`, `long: world`, `count: 2`. The key detail is what type `s` has in the match arms.

When matching on a reference (`val: &String`), Rust's match ergonomics (stabilized in Rust 1.26) automatically apply a "default binding mode" of `ref`. This means the pattern `s` doesn't move the `String` out of the vector — instead, `s` binds as `&String`.

The breakdown:
1. `&values` iterates yielding `&String` references
2. `val` is `&String`
3. In `match val { s => ... }`, since `val` is a reference and the pattern is a bare identifier, the binding mode becomes `ref`, so `s: &String`
4. Since `s` is a reference, nothing is moved, and `values` remains usable

Both options A and D describe correct output, but D is more precise: `s` is `&String`, not `String`. This is significant because it explains *why* the code compiles — no moves occur.

## Common LLM Mistakes

1. **Thinking match on a reference moves the value:** Before match ergonomics (Rust 1.26), you needed `ref s` or `&ref s` patterns. Modern Rust automatically adjusts the binding mode when matching on references.
2. **Not understanding the binding mode adjustment:** When the scrutinee is a reference, bare identifier patterns automatically become `ref` bindings. The type of `s` is `&String`, not `String`.
3. **Claiming a compilation error is needed for explicit `&` or `ref`:** Match ergonomics specifically eliminate this requirement. The old `ref` keyword is still valid but rarely needed in modern Rust.

## Key Concepts

- Match ergonomics (RFC 2005) automatically adjust binding modes when matching on references
- When the scrutinee is `&T`, a bare identifier pattern `x` binds as `&T` (effectively `ref x`)
- This prevents accidental moves out of borrowed data
- Match ergonomics were stabilized in Rust 1.26
