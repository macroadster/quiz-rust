# Problem 083: macro_rules! Matching Ambiguity and Fragment Specifiers — Solution

## Answer

**Correct: C)**

In `macro_rules!`, arms are tried in order, and the *first* matching arm is used. The `$e:expr` fragment specifier matches any valid expression — this includes literals like `42`, identifiers like `x`, and compound expressions like `1 + 2`. Since `expr` is the most general of the three specifiers and appears first, it matches *all three* invocations:

1. `what_am_i!(42)` — `42` is an expression → matches first arm → `1`
2. `what_am_i!(x)` — `x` is an expression (a path expression) → matches first arm → `1`
3. `what_am_i!(1 + 2)` — compound expression → matches first arm → `1`

The second and third arms are unreachable but do not cause a compilation error — Rust does not warn about unreachable macro arms.

## Common LLM Mistakes

1. **Assuming most-specific matching:** LLMs often assume `42` matches `$l:literal` or `x` matches `$i:ident` because they are "more specific." Rust macros use *first-match* semantics, not most-specific-match. The order of arms matters.
2. **Thinking the compiler reports ambiguity:** Unlike method resolution, macro arm matching is purely sequential. There is no ambiguity error — the first matching arm wins.
3. **Not recognizing that identifiers are expressions:** An identifier like `x` is a valid expression (a path expression), so `$e:expr` matches it even though it doesn't look like a "typical" expression.

## Key Concepts

- `macro_rules!` uses first-match semantics — arm order matters
- `$e:expr` matches any expression, including literals, identifiers, and compound expressions
- There is no "most specific match" rule for macro arms
- Unreachable macro arms are not flagged as warnings or errors
