# Problem 008: Two-Phase Borrowing — Solution

## Answer

**Correct: A)**

The code compiles and prints `[1, 2, 3, 3]`. This works because of **two-phase borrowing**, a feature of NLL. In the expression `v.push(v.len())`, Rust evaluates left-to-right: first, the method receiver `v.push(...)` creates a mutable borrow of `v`, but it enters a "reservation" phase — it's not yet activated. Then the argument `v.len()` creates an immutable borrow, which is compatible with the reserved (but not yet active) mutable borrow. `v.len()` returns `3`. The immutable borrow ends. Then the mutable borrow is *activated* for the actual `push(3)` call. The result is `[1, 2, 3, 3]`. Two-phase borrowing was introduced specifically to allow common patterns like this that would otherwise be rejected.

## Common LLM Mistakes

1. **Claiming this doesn't compile:** Many LLMs apply the simple rule that `&mut` and `&` borrows of the same value cannot coexist, concluding a compilation error. They miss two-phase borrowing, which specifically allows the mutable borrow to be in a "reserved" state while the immutable borrow for the argument is evaluated.
2. **Getting the value wrong (printing `[1, 2, 3, 4]`):** Some LLMs mistakenly compute `v.len()` as 4 (after the push) rather than 3 (before the push). Rust evaluates arguments before executing the method.
3. **Confusing evaluation order:** Some claim `push` happens before `len()` is evaluated, but Rust evaluates function/method arguments before the call.

## Key Concepts

- Two-phase borrowing allows a mutable borrow to be "reserved" while arguments are evaluated
- Arguments are evaluated before the method call is executed
- Two-phase borrowing only applies to autoref-generated `&mut` borrows (e.g., method calls)
