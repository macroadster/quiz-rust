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
