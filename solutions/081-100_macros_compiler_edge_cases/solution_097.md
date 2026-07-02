# Problem 097: Const Panic and Compile-Time Assertion — Solution

## Answer

**Correct: C)**

The program fails to compile because `const B: i32 = checked_divide(10, 0)` triggers a panic during const evaluation. Even though `B` is never *used* in `main()`, all `const` items are evaluated at compile time unconditionally.

Key points:
- `panic!` in `const fn` is allowed (stabilized in Rust 1.57) — it's only triggered if the code path is actually taken during const evaluation
- `const` items are always evaluated, even if unused — they are not lazily evaluated
- When a panic occurs during const evaluation, it becomes a **compilation error**, not a runtime error
- The error message includes the panic message: "division by zero"

This pattern is actually useful for creating compile-time assertions: if you want to ensure certain invariants at compile time, you can use `const` blocks or items that panic on invalid inputs.

## Common LLM Mistakes

1. **Thinking unused `const` items are not evaluated:** All `const` items are evaluated at compile time regardless of whether they are used. This is different from `let` bindings or function calls, which are only evaluated when reached at runtime.
2. **Thinking `panic!` is not allowed in `const fn`:** `panic!` in `const fn` has been stable since Rust 1.57. The panic only becomes an error if the panicking path is actually taken during const evaluation.
3. **Confusing const evaluation with dead code elimination:** Dead code elimination happens after compilation. Const evaluation happens *during* compilation. A `const` item must evaluate successfully even if never used.

## Key Concepts

- `panic!` is allowed in `const fn` (since Rust 1.57)
- All `const` items are evaluated at compile time, even if unused
- A panic during const evaluation becomes a compilation error, not a runtime error
- This enables compile-time assertions by deliberately panicking on invalid inputs
