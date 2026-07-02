# Problem 075: ManuallyDrop and Double-Free

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `ManuallyDrop`, `double-free`, `unsafe`, `drop`

## Problem Statement

A developer uses `ManuallyDrop` and then tries to extract the inner value.

```rust
use std::mem::ManuallyDrop;

fn main() {
    let mut s = ManuallyDrop::new(String::from("hello"));
    
    let s1 = unsafe { ManuallyDrop::take(&mut s) };
    let s2 = unsafe { ManuallyDrop::take(&mut s) };
    
    println!("{} {}", s1, s2);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `hello hello`
- B) Compilation error — cannot call `take` twice on the same `ManuallyDrop`
- C) Panics at runtime — `ManuallyDrop` tracks whether the value has been taken
- D) Undefined behavior — the second `take` reads moved-from memory, causing a double-free

## Answer

**Correct: D)**

This is **undefined behavior**. `ManuallyDrop::take` is an unsafe function that reads the value out of the `ManuallyDrop` wrapper via `ptr::read`. After the first `take`, the `ManuallyDrop` wrapper still exists in memory but its contents are logically uninitialized (the `String`'s internal pointer, length, and capacity are stale copies).

The second `take` performs `ptr::read` again, creating a **second** `String` value with the same internal pointer/length/capacity as `s1`. When `s1` and `s2` are both dropped at the end of `main`, their `Drop` implementations will both try to free the same heap allocation — a **double-free**.

`ManuallyDrop` does NOT track whether the value has been taken. It's a zero-cost wrapper — the `unsafe` on `take` is the contract that the caller must ensure the value hasn't already been taken.

## Common LLM Mistakes

1. **Claiming it prints `hello hello`:** While it might *appear* to work on some runs (UB can do that), this is a double-free and is definitively UB.
2. **Claiming ManuallyDrop tracks state (option C):** `ManuallyDrop` is a `#[repr(transparent)]` wrapper with zero runtime overhead. It has no flag or state tracking — the safety invariant is entirely the caller's responsibility.
3. **Claiming it's a compile error:** The borrow checker sees two `&mut s` borrows that don't overlap (each `take` call borrows and returns), so this compiles fine. The problem is purely semantic.

## Key Concepts

- `ManuallyDrop::take` is unsafe because calling it twice is UB (double-free)
- `ManuallyDrop` has no runtime tracking — it's a zero-cost wrapper
- `ptr::read` creates a bitwise copy without marking the source as moved
- The caller must guarantee `take` is called at most once
