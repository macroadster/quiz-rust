# Problem 042: When Does a Type Lose Send?

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Send`, `Sync`, `auto-traits`, `PhantomData`

## Problem Statement

Consider these type definitions and the function that follows:

```rust
use std::marker::PhantomData;

struct MyWrapper<T> {
    value: i64,
    _marker: PhantomData<T>,
}

fn require_send<T: Send>(_t: T) {}

fn main() {
    let w1: MyWrapper<i32> = MyWrapper { value: 42, _marker: PhantomData };
    let w2: MyWrapper<*mut u8> = MyWrapper { value: 42, _marker: PhantomData };
    let w3: MyWrapper<std::rc::Rc<i32>> = MyWrapper { value: 42, _marker: PhantomData };

    require_send(w1);  // Line A
    require_send(w2);  // Line B
    require_send(w3);  // Line C
}
```

## Question

Which lines fail to compile?

## Options

- A) All three lines compile successfully
- B) Only Line B fails (raw pointers are not `Send`)
- C) Lines B and C fail (`*mut u8` and `Rc<i32>` are not `Send`)
- D) Only Line C fails (`Rc<i32>` is not `Send`, but `*mut u8` is `Send`)
