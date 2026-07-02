# Problem 002: Move Semantics in Closures with FnOnce — Solution

## Answer

**Correct: C)**

The code compiles and prints `10`, `10`, `hello`. The `move` keyword causes the closure to take ownership of any captured variables by moving or copying them. However, the closure body only references `n`, not `s`. Therefore, only `n` is captured. Since `n` is `u32` (a `Copy` type), it is copied into the closure — the original `n` remains valid. The variable `s` is never captured by the closure because it is not referenced inside the closure body. The `move` keyword does not blindly capture every variable in scope — it only affects variables that are actually used inside the closure, forcing them to be moved (or copied) rather than borrowed. The closure only calls `println!("{}", n)`, which makes it implement `Fn` (not just `FnOnce`), so it can be called multiple times. `s` remains valid and can be printed after the closure calls.

## Common LLM Mistakes

1. **Thinking `move` captures all variables in scope:** LLMs frequently claim that `move` causes the closure to take ownership of every local variable, including `s`. In reality, `move` only affects variables that are actually referenced inside the closure body.
2. **Confusing `move` with `FnOnce`:** The `move` keyword controls *how* variables are captured (by move/copy instead of by reference), not *how many times* the closure can be called. A `move` closure that only copies `Copy` types still implements `Fn`.
3. **Missing that `Copy` types inside `move` closures are copied:** Even with `move`, a `u32` is copied into the closure, not moved, because it implements `Copy`.

## Key Concepts

- `move` closures only capture variables that are actually referenced in the closure body
- `move` controls capture mode (move/copy vs borrow), not the `Fn`/`FnMut`/`FnOnce` trait
- `Copy` types are copied even inside `move` closures
