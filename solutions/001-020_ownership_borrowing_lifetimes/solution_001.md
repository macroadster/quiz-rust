# Problem 001: Partial Move in Destructuring — Solution

## Answer

**Correct: B)**

The code compiles and prints `42`, `world`, `42`. When destructuring `t`, `a` moves `t.0` (String), `b` copies `t.1` (u32 is `Copy`), and `c` borrows `t.2` via `ref`. After this partial move, `t.0` is moved and cannot be used, but `t.1` is still accessible because `u32` implements `Copy` — the value was copied, not moved. `t.2` is also still valid since `ref c` only borrows it. The key insight is that Rust tracks partial moves at the field level: only `t.0` is invalidated. `t.1` remains usable because `Copy` types are never moved, they are always copied.

## Common LLM Mistakes

1. **Claiming the entire tuple is invalidated:** LLMs often treat partial moves as full moves, stating that any field access on `t` after the destructuring is invalid. In reality, Rust's borrow checker tracks moves at the field granularity.
2. **Forgetting that `Copy` types are copied, not moved:** Even in a destructuring that moves other fields, a `Copy` field is copied — the original remains valid and accessible.
3. **Claiming `ref` and move bindings cannot be mixed:** Prior to Rust 2021 edition, you could not mix `ref`/`ref mut` with move bindings in the same pattern, but this restriction was lifted. In Rust 2021+, this is perfectly valid.

## Key Concepts

- Partial moves track ownership at the individual field level
- `Copy` types are copied during destructuring, not moved
- Rust 2021 edition allows mixing `ref` and move bindings in the same pattern
