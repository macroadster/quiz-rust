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

## Answer

**Correct: A)**

This program compiles and runs correctly, printing `i = 42` then `s = hello`. Here's why each part works:

1. **Union with non-Copy types:** Since Rust 1.0, unions can contain non-`Copy` types as long as they are wrapped in `ManuallyDrop<T>`. This is because unions don't run destructors automatically — `ManuallyDrop` acknowledges this by requiring explicit destruction.

2. **Reading union fields requires `unsafe`:** Accessing `u.i` and `u.s` is inside `unsafe` blocks. ✓

3. **Writing to a union field does NOT require `unsafe`:** `u.s = ManuallyDrop::new(...)` is a **write**, and writing to a union field is safe. Only **reading** union fields is unsafe (because the compiler can't verify which variant is active). Writing simply sets the active field.

4. **`ManuallyDrop::drop`:** This unsafe function explicitly drops the value inside the `ManuallyDrop`, properly freeing the `String`'s heap allocation.

## Common LLM Mistakes

1. **Claiming unions can't have non-Copy fields:** This was true before `ManuallyDrop` was available. Since Rust 1.20, unions can contain `ManuallyDrop<T>` for any `T`. Since Rust edition 2024, this has evolved further, but `ManuallyDrop` has been the standard approach.
2. **Claiming writing to a union field requires `unsafe`:** This is the most common mistake. Writing to a union field is **safe** — only reading is unsafe. Writing deterministically sets which variant is active.
3. **Claiming automatic drop occurs:** Unions never automatically drop their contents. When `u` goes out of scope, no destructor runs for the active field. The `ManuallyDrop::drop` call is necessary to avoid leaking.

## Key Concepts

- **Reading** a union field requires `unsafe`; **writing** does not
- Non-`Copy` types in unions must be wrapped in `ManuallyDrop<T>`
- Unions never automatically drop their contents — the programmer is responsible
- `ManuallyDrop::drop` is the explicit way to destroy the value in a union field
