# Problem 073: Struct Field Drop Order — Solution

## Answer

**Correct: A)**

Struct fields are dropped in **declaration order**, not initialization order. Regardless of the order in which fields are assigned in the struct literal (`second`, `first`, `third`), the drop order follows the order fields appear in the struct **definition**: `first`, `second`, `third`.

So the output is `A B C`.

This is guaranteed by the Rust Reference: "The fields of a struct are dropped in declaration order." This is a deliberate design choice that provides predictable resource cleanup. Note this is different from local variables, which are dropped in *reverse* declaration order.

## Common LLM Mistakes

1. **Claiming reverse declaration order (`C B A`):** LLMs often confuse struct field drop order with local variable drop order. Local variables drop in reverse order; struct fields drop in **forward** declaration order.
2. **Claiming initialization order (`B A C`):** The order of field expressions in the struct literal is irrelevant to drop order. All fields are fully initialized before the struct value exists.
3. **Claiming it's unspecified:** Struct field drop order IS specified in the Rust Reference. It is declaration order and has been stable since Rust 1.0.

## Key Concepts

- Struct fields drop in **declaration order** (forward, not reverse)
- Local variables drop in **reverse declaration order** (opposite convention)
- The order of field expressions in a struct literal does not affect drop order
- Struct field drop order is guaranteed and stable — safe to rely on
