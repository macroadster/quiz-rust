# Problem 016: Self-Referential Struct — The Pin Problem — Solution

## Answer

**Correct: B)**

This code exhibits **undefined behavior**. Inside `new()`, the struct `s` is created on the stack and `data_ref` is set to point to `s.data`'s address on that stack frame. When the function returns `s`, the struct is **moved** to the caller's stack frame (or wherever `let s = SelfRef::new(...)` places it). After the move, `s.data` is at a new memory address, but `s.data_ref` still holds the *old* pointer from inside `new()`'s stack frame. Dereferencing `data_ref` in `get_ref()` accesses invalid memory — this is undefined behavior.

The code compiles without errors because raw pointers (`*const T`) bypass the borrow checker. The `unsafe` block in `get_ref()` tells the compiler "trust me," but the invariant is violated. This is exactly why self-referential structs are so hard in Rust: moves invalidate internal pointers. This is the problem that `Pin<T>` was designed to address — by guaranteeing a value will not be moved, `Pin` makes it safe to hold self-references.

## Common LLM Mistakes

1. **Claiming it prints `hello`:** LLMs often trace through the code logically and assume the pointer remains valid, not accounting for the move that occurs when returning from `new()`. In practice, this *may* appear to work in debug builds due to stack frame reuse, but it is UB.
2. **Claiming it's a compilation error:** Raw pointers and `unsafe` blocks are perfectly valid Rust. The compiler does not catch this bug — that's the point of `unsafe`.
3. **Underestimating the move:** LLMs sometimes think `let s = SelfRef::new(...)` is an "optimization" that elides the move (like guaranteed copy elision in C++17). Rust does not guarantee this — the move semantics are fundamental.

## Key Concepts

- Moving a struct invalidates any internal raw pointers to its own fields
- Raw pointers bypass the borrow checker — `unsafe` code must manually uphold invariants
- `Pin<T>` exists to prevent moves, enabling safe self-referential structures
