# Problem 068: repr(transparent) and Newtype FFI

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `repr-transparent`, `newtype`, `ffi`, `layout`

## Problem Statement

A developer creates newtype wrappers and tries to pass them to an `extern "C"` function.

```rust
use std::mem;

#[repr(transparent)]
struct Meters(f64);

struct Seconds(f64);

extern "C" {
    fn some_c_function(distance: f64) -> f64;
}

fn main() {
    println!("Meters size: {}", mem::size_of::<Meters>());
    println!("Seconds size: {}", mem::size_of::<Seconds>());

    let m = Meters(100.0);
    let s = Seconds(9.58);

    // Which of these is guaranteed safe for FFI?
    // let _ = unsafe { some_c_function(m.0) };           // Line A
    // let _ = unsafe { some_c_function(mem::transmute(m)) }; // Line B
    // let _ = unsafe { some_c_function(mem::transmute(s)) }; // Line C
}
```

## Question

Which of the uncommented calls would be valid if uncommented? (Assume `some_c_function` is correctly linked.)

## Options

- A) Only Line A is valid
- B) Lines A and B are valid; Line C is undefined behavior
- C) All three lines are valid — both structs have the same layout as `f64`
- D) Lines A and B are valid; Line C is a compilation error
