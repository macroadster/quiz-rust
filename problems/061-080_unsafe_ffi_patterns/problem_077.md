# Problem 077: Unwinding Across extern "C" Boundaries

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `FFI`, `extern-C`, `panic`, `unwinding`, `ABI`

## Problem Statement

A developer writes a callback function that may panic, and exposes it via `extern "C"`.

```rust
extern "C" fn callback(x: i32) -> i32 {
    if x < 0 {
        panic!("negative input!");
    }
    x * 2
}

fn main() {
    let result = callback(-1);
    println!("{}", result);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Panics with the message "negative input!" and unwinds normally
- B) Undefined behavior — panicking across an `extern "C"` boundary is UB
- C) Compilation error — `extern "C"` functions cannot use `panic!`
- D) The program aborts — panics in `extern "C"` functions cause an immediate abort

## Answer

**Correct: D)**

Since Rust 1.71 (stabilizing behavior that was a warning since 1.24), a panic that would unwind out of an `extern "C"` function causes an **immediate abort**. The program does not unwind — it terminates.

The reasoning: the C ABI has no concept of Rust's unwinding mechanism. If a panic were allowed to unwind through C frames (or through any `extern "C"` boundary), it would be undefined behavior because the C calling convention doesn't support stack unwinding in the way Rust's `extern "Rust"` ABI does.

Rust prevents this UB by catching the unwind at the `extern "C"` boundary and aborting the process. You'll see output like:

```
thread 'main' panicked at 'negative input!'
note: panic in a function that cannot unwind
thread caused non-unwinding panic. aborting.
```

If you need unwinding across FFI, use `extern "C-unwind"` (stabilized in Rust 1.71).

## Common LLM Mistakes

1. **Claiming it's UB:** In *older* Rust versions, this was indeed UB. Since Rust 1.71, the language guarantees an abort instead of UB. LLMs trained on older information get this wrong.
2. **Claiming normal unwinding occurs:** `extern "C"` functions do not support unwinding. The panic is caught at the boundary and the process aborts.
3. **Claiming it's a compile error (option C):** Rust allows `panic!` inside `extern "C"` functions syntactically. The abort behavior is a runtime mechanism, not a compile-time restriction.

## Key Concepts

- Panics in `extern "C"` functions cause an **abort**, not unwinding (since Rust 1.71)
- `extern "C-unwind"` is the ABI that permits unwinding across C boundaries
- This abort behavior prevents the UB of unwinding through C frames
- The abort happens at the `extern "C"` function boundary, not at the panic site
