# Problem 070: Shared Reference to Mutable Data via UnsafeCell

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `UnsafeCell`, `interior-mutability`, `aliasing`, `stacked-borrows`

## Problem Statement

A developer uses `UnsafeCell` for interior mutability and creates aliasing pointers.

```rust
use std::cell::UnsafeCell;

fn main() {
    let cell = UnsafeCell::new(42i32);

    let r1: &i32 = unsafe { &*cell.get() };
    let r2: *mut i32 = cell.get();

    unsafe {
        *r2 = 100;
    }

    println!("{}", r1);
}
```

## Question

What happens when this program is compiled and run?

## Options

- A) Prints `100`
- B) Undefined behavior — writing through `r2` while `r1` (a shared reference) is live
- C) Prints `42`
- D) Compilation error — cannot create `&i32` from `UnsafeCell`

## Answer

**Correct: B)**

This is UB under the Stacked Borrows model (and the newer Tree Borrows model). The issue is subtle:

1. `cell.get()` returns a `*mut i32`. 
2. `&*cell.get()` dereferences the raw pointer and creates a **shared reference** `r1: &i32`. Creating this shared reference pushes a `SharedRO` permission onto the borrow stack.
3. `cell.get()` is called again, returning a new `*mut i32`. 
4. Writing through `r2` (`*r2 = 100`) performs a write access, which **invalidates** the `SharedRO` tag on the borrow stack — it pops `r1`'s permission.
5. Reading through `r1` in `println!` then uses an invalidated tag — this is UB.

The fundamental error is creating a `&i32` (shared reference, which promises immutability) and then mutating the data while that reference is live. Even though `UnsafeCell` allows interior mutability, the correct approach is to only use *raw pointers* — never create a `&i32` shared reference if you plan to mutate through another pointer.

## Common LLM Mistakes

1. **Claiming it prints `100` because UnsafeCell allows mutation:** `UnsafeCell` removes the compiler optimization assumption at the *cell* level, but creating a `&i32` (not `&UnsafeCell<i32>`) re-establishes the immutability guarantee. The `&i32` reference is what makes this UB.
2. **Claiming it compiles without UB because UnsafeCell is designed for this:** UnsafeCell enables interior mutability, but you must not create shared references (`&T`) to the interior if you plan to mutate. Use raw pointers instead.
3. **Missing that `r1` is a `&i32`, not a `&UnsafeCell<i32>`:** The `&*` pattern dereferences past the `UnsafeCell`, creating a plain shared reference with full immutability guarantees.

## Key Concepts

- `UnsafeCell` only suspends aliasing guarantees for `&UnsafeCell<T>`, not for `&T`
- Creating `&i32` from an `UnsafeCell<i32>` re-establishes the no-mutation guarantee
- Under Stacked Borrows, writing through a raw pointer invalidates live shared references
- Safe interior mutability requires working exclusively with raw pointers or going through proper abstractions
