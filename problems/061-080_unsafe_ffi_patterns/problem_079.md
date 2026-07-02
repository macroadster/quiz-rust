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
