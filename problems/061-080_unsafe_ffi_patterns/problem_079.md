# Problem 079: PhantomData and Drop Check

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `PhantomData`, `drop-check`, `lifetimes`, `unsafe`

## Problem Statement

A developer creates a custom smart pointer using raw pointers and `PhantomData`, and tests what the drop checker allows.

```rust
use std::marker::PhantomData;

struct RawBox<T> {
    ptr: *mut T,
    _owns: PhantomData<T>,
}

impl<T> RawBox<T> {
    fn new(val: T) -> Self {
        let ptr = Box::into_raw(Box::new(val));
        RawBox { ptr, _owns: PhantomData }
    }
}

impl<T> Drop for RawBox<T> {
    fn drop(&mut self) {
        unsafe { drop(Box::from_raw(self.ptr)); }
    }
}

fn main() {
    let s = String::from("hello");
    let rb = RawBox::new(&s);
    drop(s);
    println!("done");
}
```

## Question

What happens when this program is compiled?

## Options

- A) Compiles and prints `done` (then drops `rb` which double-frees)
- B) Compiles and prints `done` (no double-free because `rb` holds a `&&String`)
- C) Compilation error — `s` cannot be dropped while `rb` holds a reference to it
- D) Compilation error — `RawBox` cannot store a reference type

## Answer

**Correct: C)**

This does not compile. The key is the `PhantomData<T>` field with a `Drop` implementation.

When `T = &'a String`, `RawBox<&'a String>` contains `PhantomData<&'a String>`. Because `RawBox` implements `Drop`, the **drop checker** requires that all types "owned" by the struct (indicated by `PhantomData<T>`) must be valid when the destructor runs.

Since `rb: RawBox<&String>` logically owns a `&String` referencing `s`, the drop checker prevents `s` from being dropped before `rb`. The `drop(s)` on the line before `rb`'s implicit drop is rejected:

```
error[E0505]: cannot move out of `s` because it is borrowed
```

If `_owns` were `PhantomData<*const T>` instead of `PhantomData<T>`, the drop checker would NOT enforce this — raw pointers don't participate in drop check. The choice of `PhantomData<T>` is deliberate for types like `Box<T>` that genuinely own `T` values.

## Common LLM Mistakes

1. **Claiming it compiles because raw pointers have no lifetime:** The raw pointer `*mut T` itself has no lifetime tracking, but `PhantomData<T>` tells the drop checker that `RawBox` **owns** a `T`. This is the entire purpose of the `PhantomData` field.
2. **Ignoring drop check:** LLMs often forget that `impl Drop` activates drop check, which examines `PhantomData` fields to determine what lifetimes must be valid at drop time.
3. **Claiming `PhantomData` is only for variance:** `PhantomData<T>` serves double duty: it affects variance AND drop check. For drop check, it tells the compiler that the type logically owns a `T`.

## Key Concepts

- `PhantomData<T>` tells the drop checker that the type logically owns a `T`
- Types with `impl Drop` that own a `T` (via PhantomData) must ensure `T` is valid at drop time
- This prevents use-after-free in custom smart pointers
- `PhantomData<*const T>` opts out of drop check for `T` (raw pointer semantics)
