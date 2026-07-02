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

## Answer

**Correct: A)**

This program is well-defined and prints `20`. Here's why:

Both `p1` and `p2` are copies of the same raw pointer `ptr`, which was derived from `&mut x`. Under Stacked Borrows, both `p1` and `p2` carry the **same tag** (because they're copies, not re-borrows). The borrow stack looks like:

1. `x` (base)
2. `&mut x` tag (from the mutable borrow)
3. `ptr` tag (derived from the mutable reference)

Since `p1` and `p2` are just copies of `ptr`, they share the same tag. Writing through either one is accessing with the same tag, so neither write invalidates the other. After `*p2 = 20`, reading `*p1` reads the current value of `x`, which is `20`.

This contrasts with creating two *separate* `&mut x` references (which would be rejected by the borrow checker) or creating a new raw pointer via a fresh mutable reborrow.

## Common LLM Mistakes

1. **Claiming this is UB due to aliasing:** Raw pointers are explicitly allowed to alias in Rust. The key question is whether the *tags* are compatible under Stacked Borrows, and here they are because both pointers are copies of the same original.
2. **Claiming it prints `10`:** After `*p2 = 20`, the value at that address is `20`. Since `p1` and `p2` point to the same address with the same tag, `*p1` reads `20`.
3. **Confusing this with the `&mut x` / `&mut x` case (Problem 062):** Unlike creating two `&mut x` expressions, copying a raw pointer does not create new borrows or new tags.

## Key Concepts

- Copying a raw pointer (`let p2 = ptr`) copies the tag — both pointers have identical provenance
- Raw pointer aliasing is allowed; Stacked Borrows tracks *tags*, not pointer identity
- Writes through one copy of a pointer do not invalidate other copies with the same tag
- This differs fundamentally from creating separate mutable references
