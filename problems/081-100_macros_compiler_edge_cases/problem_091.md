# Problem 091: Deref Coercion Chain and Method Dispatch

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `Deref`, `method-dispatch`, `autoderef`, `String`, `smart-pointers`

## Problem Statement

A developer calls a method through a chain of smart pointers:

```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> Deref for MyBox<T> {
    type Target = T;
    fn deref(&self) -> &T {
        &self.0
    }
}

fn main() {
    let s = MyBox(MyBox(MyBox(String::from("hello"))));
    let len = s.len();
    println!("{}", len);
}
```

## Question

What is the output of this program?

## Options

- A) Prints `5`
- B) Compilation error — `MyBox<MyBox<MyBox<String>>>` doesn't have a `len()` method
- C) Compilation error — deref coercion is limited to one level
- D) Compilation error — `MyBox` doesn't implement `len()`
