# Problem 061: Raw Pointer Round-Trip Through Integer

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `raw-pointers`, `pointer-provenance`, `as-casts`

## Problem Statement

A developer stores a pointer as an integer and then converts it back to a pointer for dereferencing.

```rust
fn main() {
    let x: i32 = 42;
    let ptr = &x as *const i32;
    let addr = ptr as usize;
    let ptr2 = addr as *const i32;

    unsafe {
        println!("{}", *ptr2);
    }
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `42`
- B) Undefined behavior — pointer provenance is lost during the integer round-trip
- C) Compilation error — cannot cast `usize` to `*const i32`
- D) Undefined behavior — `x` is on the stack and the pointer is dangling
