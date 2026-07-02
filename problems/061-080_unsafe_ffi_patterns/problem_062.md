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
