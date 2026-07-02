# Problem 081: Macro Hygiene and Variable Capture — Solution

## Answer

**Correct: B)**

Rust's `macro_rules!` macros use a hygiene system that prevents accidental variable capture. The identifier `x` inside the macro definition is in the macro's *definition-site* hygiene context, not the *call-site* context. Even though there is a variable `x` in `main()`, the macro's reference to `x` exists in a different syntactic context, so the compiler reports an error like `cannot find value 'x' in this scope`.

This is a fundamental difference between Rust macros and C preprocessor macros. In C, `#define ADD_TO_X(val) (x + val)` would work because C macros are purely textual substitution. Rust macros are *syntactic* and respect hygiene boundaries.

Note: The `$val` parameter *does* bridge the hygiene gap — expressions passed as macro arguments carry their call-site context. But bare identifiers written directly in the macro body do not.

## Common LLM Mistakes

1. **Assuming Rust macros are textual substitution like C:** LLMs frequently treat `macro_rules!` as a simple text replacement, concluding the code prints `15`. Rust macros are hygienic — identifiers in the macro body cannot capture variables from the call site.
2. **Confusing macro parameters with macro body identifiers:** The parameter `$val` correctly refers to the call-site expression, but the bare `x` in the macro body is in the macro's own hygiene context.
3. **Thinking hygiene only applies to `let` bindings inside macros:** Hygiene applies to *all* identifier references in the macro body, not just newly introduced bindings.

## Key Concepts

- Rust `macro_rules!` macros are hygienic — identifiers in the macro body cannot capture call-site variables
- Macro parameters (`$val`) bridge the hygiene gap; bare identifiers in the body do not
- This is fundamentally different from C preprocessor textual substitution
