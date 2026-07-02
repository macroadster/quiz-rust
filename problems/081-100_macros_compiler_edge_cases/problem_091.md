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

## Answer

**Correct: A)**

Rust's autoderef mechanism follows the `Deref` chain as many levels as needed to find a matching method. Here's the deref chain:

1. `s` is `MyBox<MyBox<MyBox<String>>>`
2. Deref to `MyBox<MyBox<String>>`
3. Deref to `MyBox<String>`
4. Deref to `String`
5. `String` implements `Deref<Target = str>`, deref to `str`
6. `str` has a `len()` method — found!

The compiler applies `.deref()` three times to get to `String`, then once more to reach `str`, and calls `str::len()`. The string `"hello"` has length 5.

Deref coercion has no depth limit — the compiler follows the entire chain until it either finds the method or exhausts all possibilities. Each `Deref` impl is applied in sequence, and the method resolution algorithm searches at each level.

## Common LLM Mistakes

1. **Thinking deref coercion has a depth limit:** There is no limit on the number of deref steps. The compiler will follow the chain as far as needed. (In practice, infinite chains are prevented because each `Deref` impl produces a different type.)
2. **Forgetting `String` derefs to `str`:** The chain doesn't stop at `String` — `String` implements `Deref<Target = str>`, and `len()` is a method on `str`, not `String` directly.
3. **Claiming `MyBox` needs to implement `len()`:** Deref coercion specifically exists so wrapper types don't need to re-implement methods of their inner types.

## Key Concepts

- Rust's autoderef follows the full `Deref` chain with no depth limit
- Method resolution searches at each level of the deref chain
- `String` derefs to `str`, where methods like `len()` are defined
- Deref coercion enables transparent use of wrapper/smart pointer types
