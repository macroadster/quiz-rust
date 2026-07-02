# Problem 016: Self-Referential Struct — The Pin Problem

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `self-referential`, `Pin`, `unsafe`, `move-semantics`

## Problem Statement

A developer attempts to create a self-referential struct:

```rust
struct SelfRef {
    data: String,
    data_ref: *const String,
}

impl SelfRef {
    fn new(data: String) -> Self {
        let mut s = SelfRef {
            data,
            data_ref: std::ptr::null(),
        };
        s.data_ref = &s.data as *const String;
        s
    }

    fn get_ref(&self) -> &String {
        unsafe { &*self.data_ref }
    }
}

fn main() {
    let s = SelfRef::new(String::from("hello"));
    println!("{}", s.get_ref());
}
```

## Question

What happens when this code is executed?

## Options

- A) Prints `hello` — the raw pointer correctly points to the struct's own `data` field
- B) Undefined behavior — `data_ref` points to the old location of `data` before the struct was moved out of `new()`
- C) Compilation error: cannot create a raw pointer to a local variable
- D) Compilation error: `SelfRef` cannot implement methods that return references derived from raw pointers
