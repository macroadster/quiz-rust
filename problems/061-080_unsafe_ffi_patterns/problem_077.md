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
