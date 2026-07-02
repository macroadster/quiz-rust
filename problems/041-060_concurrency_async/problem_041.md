# Problem 041: The Send Bound Surprise with Rc

**Difficulty:** ⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Send`, `Rc`, `Arc`, `thread::spawn`

## Problem Statement

A developer wants to share data across threads. Consider the following code:

```rust
use std::rc::Rc;
use std::thread;

fn main() {
    let data = Rc::new(vec![1, 2, 3]);
    let data_clone = Rc::clone(&data);

    let handle = thread::spawn(move || {
        println!("{:?}", data_clone);
    });

    println!("{:?}", data);
    handle.join().unwrap();
}
```

## Question

What happens when you try to compile this code?

## Options

- A) It compiles and prints `[1, 2, 3]` twice
- B) Compilation error: `Rc<Vec<i32>>` cannot be sent between threads safely because it doesn't implement `Send`
- C) Compilation error: `data` is moved into the closure and cannot be used afterward
- D) It compiles but panics at runtime due to a data race on the reference count
