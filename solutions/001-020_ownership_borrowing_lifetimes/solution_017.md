# Problem 017: Why Lifetime-Based Self-Referential Structs Don't Work — Solution

## Answer

**Correct: C)**

The code does not compile. The error is that `data` does not live long enough. In the function `new`, `data` is a parameter (local variable). The expression `&data` creates a reference to this local. When we try to assign `slice: &data`, the reference has the lifetime of the local `data` inside `new()`. But the struct `SelfRef<'a>` needs `slice` to live for `'a`, which extends beyond `new()` (into the caller's scope). The local `data` is moved into `self.data`, but the reference `&data` was to the *parameter*, not to `self.data`. After the move, the parameter `data` is consumed, and the reference is invalid.

Even if we rearranged the code to first move `data` into the struct and then try to reference `self.data`, we can't: you can't borrow from an owned value while simultaneously moving the whole struct. This is the fundamental reason safe self-referential structs are impossible in Rust: the lifetime of the reference cannot be expressed relative to the struct that owns the data.

## Common LLM Mistakes

1. **Thinking field initialization order matters:** Some LLMs suggest reordering the fields or using a two-step initialization would fix this. It doesn't — the fundamental issue is that no lifetime `'a` can express "lives as long as the struct itself."
2. **Confusing `data` the parameter with `self.data` the field:** The reference `&data` is to the function parameter, which is moved (consumed) when assigned to the struct field. The reference doesn't magically update to point to the struct's field.
3. **Choosing option D:** There's no general rule against struct fields referencing each other. The issue is specifically about *lifetimes* — Rust has no way to express a "self-referencing" lifetime.

## Key Concepts

- References to function parameters cannot outlive the function call
- Rust has no way to express a lifetime that means "the same as the struct's own lifetime"
- Self-referential structs require either `Pin` + `unsafe`, or crate-level abstractions like `ouroboros` or `self_cell`
