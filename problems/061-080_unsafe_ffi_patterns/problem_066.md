# Problem 066: Transmuting Enums and Niche Optimization

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `transmute`, `enum-layout`, `niche-optimization`, `undefined-behavior`

## Problem Statement

A developer tries to exploit niche optimization for `Option<bool>`.

```rust
use std::mem;

fn main() {
    let x: u8 = 2;
    let opt: Option<bool> = unsafe { mem::transmute(x) };

    match opt {
        Some(true) => println!("true"),
        Some(false) => println!("false"),
        None => println!("none"),
    }
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `none` because `2` is the niche value for `None`
- B) Compilation error — `u8` and `Option<bool>` have different sizes
- C) Prints `true` because any non-zero value is truthy
- D) Undefined behavior — `2` is not a valid bit pattern for `Option<bool>`
