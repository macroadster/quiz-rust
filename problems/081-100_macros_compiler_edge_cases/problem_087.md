# Problem 087: Integer Literal Type Fallback

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `type-inference`, `integer-fallback`, `i32`, `type-ambiguity`

## Problem Statement

A developer writes code that relies on integer literal type inference with a generic function:

```rust
fn print_type_of<T>(_: &T) {
    println!("{}", std::any::type_name::<T>());
}

fn main() {
    let x = 42;
    let y = x + 0u8;
    print_type_of(&y);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `u8`
- B) Prints `i32`
- C) Compilation error — cannot add `i32` and `u8`
- D) Compilation error — ambiguous integer type for `x`

## Answer

**Correct: A)**

The integer literal `42` is initially an unconstrained integer type variable — it is *not* yet `i32`. The `i32` fallback only applies when there are zero constraints on the type by the end of type inference.

When the compiler encounters `x + 0u8`, it looks for an `Add` impl. The built-in integer `Add` impls require both operands to be the same type. Since `0u8` is concretely `u8`, the compiler unifies `x`'s type variable with `u8`, making `x: u8`. The result of the addition is also `u8`, so `y: u8`.

The program compiles and prints `u8`.

## Common LLM Mistakes

1. **Assuming integer literals always become `i32`:** The `i32` fallback only applies when there are *zero* type constraints on the literal. Any constraint — from operations, function arguments, or type annotations — overrides the fallback. LLMs frequently answer C) thinking `x` is `i32` and then `i32 + u8` is a type mismatch.
2. **Thinking Rust assigns types sequentially:** The compiler doesn't assign `i32` to `x` on one line and then check the next line. Type inference is constraint-based — all constraints are collected and solved simultaneously.
3. **Confusing unconstrained literals with `i32`:** An unconstrained integer literal is a type variable `{integer}`, not `i32`. It only *falls back* to `i32` if nothing else constrains it.

## Key Concepts

- Integer literals in Rust have unconstrained integer type variables, not `i32`
- The `i32` fallback only applies when no constraints exist at the end of type inference
- Operations with typed values constrain otherwise-unconstrained integer literals
- Rust's type inference is constraint-based, not sequential
