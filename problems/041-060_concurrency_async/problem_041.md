# Problem 041: Why &Cell Is !Send, Yet Mutex<Cell> Is Sync

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Concurrency & Async  
**Tags:** `Send`, `Sync`, `Cell`, `Mutex`, `auto-traits`, `shared-references`

## Problem Statement

A developer is mapping the `Send` / `Sync` lattice for interior mutability and mutexes. Consider these helper bounds and four call sites:

```rust
use std::cell::Cell;
use std::rc::Rc;
use std::sync::Mutex;

fn require_send<T: Send>(_: T) {}
fn require_sync<T: Sync>(_: T) {}

fn main() {
    let cell = Cell::new(0i32);
    require_send(cell);                       // Line A

    let cell = Cell::new(0i32);
    require_send(&cell);                      // Line B

    require_sync(Mutex::new(Cell::new(0i32))); // Line C
    require_sync(Mutex::new(Rc::new(0i32)));   // Line D
}
```

## Question

Which lines fail to compile?

## Options

- A) Lines B and D
- B) Lines B, C, and D (`Mutex<T>: Sync` requires `T: Sync`, so wrapping `Cell` cannot help)
- C) Lines A, B, and D (`Cell<T>` is never `Send`, same as `Rc<T>`)
- D) Only Line D (`&Cell<i32>` is `Send` because `Cell<i32>` is `Send`)
