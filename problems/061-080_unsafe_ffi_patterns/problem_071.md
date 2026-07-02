# Problem 071: Reborrowing Raw Pointers and Stacked Borrows

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `stacked-borrows`, `raw-pointers`, `aliasing`, `miri`

## Problem Statement

A developer carefully derives two raw pointers from the same mutable reference.

```rust
fn main() {
    let mut x: i32 = 0;
    let ptr = &mut x as *mut i32;

    let p1 = ptr;
    let p2 = ptr;

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

- A) Prints `20`
- B) Prints `10`
- C) Undefined behavior — `p1` and `p2` are aliasing mutable raw pointers
- D) Undefined behavior — writing through `p2` invalidates `p1` under Stacked Borrows
