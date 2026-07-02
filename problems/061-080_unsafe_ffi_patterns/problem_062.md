# Problem 062: Multiple Mutable Raw Pointers

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `raw-pointers`, `aliasing`, `mutable-pointers`

## Problem Statement

A developer creates two mutable raw pointers to the same variable and writes through both.

```rust
fn main() {
    let mut x: i32 = 1;
    let p1 = &mut x as *mut i32;
    let p2 = &mut x as *mut i32;

    unsafe {
        *p1 = 10;
        *p2 = 20;
        println!("{}", *p1);
    }
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `10`
- B) Prints `20`
- C) Undefined behavior — two mutable raw pointers alias
- D) Compilation error — cannot create two `&mut x` borrows

## Answer

**Correct: D)**

This fails to compile. The issue is **not** with the raw pointers themselves — it's with the creation of two `&mut x` references. Each `&mut x` expression creates a mutable reference, and the first `&mut x` borrows `x` mutably. When the second `&mut x` attempts to borrow `x` mutably again, the borrow checker rejects it because `p1` still holds a pointer derived from the first mutable borrow, and raw pointers keep their source borrows alive.

The correct way to create two mutable raw pointers is: `let p1 = &mut x as *mut i32; let p2 = p1;` or using `addr_of_mut!(x)`.

```
error[E0499]: cannot borrow `x` as mutable more than once at a time
```

## Common LLM Mistakes

1. **Claiming it compiles and prints `20`:** LLMs often assume raw pointers bypass the borrow checker entirely. While *dereferencing* raw pointers is unchecked, *creating* them from references still goes through normal borrow-checking of the reference expression.
2. **Claiming UB from aliasing:** This doesn't even compile, so UB analysis is moot. Even if it did compile (e.g., via `addr_of_mut!`), writing through two raw pointers derived from the same place is permitted as long as no references are live.
3. **Confusing raw pointer creation with raw pointer use:** The borrow checker applies to `&mut x` regardless of whether it's immediately cast to `*mut i32`.

## Key Concepts

- Creating `&mut x` is always borrow-checked, even when immediately cast to a raw pointer
- Raw pointers derived from mutable references keep the borrow alive
- Use `std::ptr::addr_of_mut!` to create raw pointers without going through a reference
