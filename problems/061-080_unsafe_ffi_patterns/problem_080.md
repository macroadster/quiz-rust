# Problem 080: Union Types and Unsafe Field Access

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `union`, `unsafe`, `Copy`, `ManuallyDrop`

## Problem Statement

A developer creates a union with both `Copy` and non-`Copy` fields.

```rust
use std::mem::ManuallyDrop;

union MyUnion {
    i: i32,
    s: ManuallyDrop<String>,
}

fn main() {
    let mut u = MyUnion { i: 42 };

    unsafe {
        println!("i = {}", u.i);
    }

    u.s = ManuallyDrop::new(String::from("hello"));

    unsafe {
        println!("s = {}", u.s);
        ManuallyDrop::drop(&mut u.s);
    }
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `i = 42` then `s = hello`
- B) Compilation error — unions cannot contain non-`Copy` types like `String`
- C) Compilation error — assigning to `u.s` requires an `unsafe` block
- D) Undefined behavior — writing to `u.s` without `unsafe` is unsound
