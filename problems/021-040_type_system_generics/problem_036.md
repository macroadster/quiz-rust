# Problem 036: Send and Sync Propagation with Rc and Arc

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `Send`, `Sync`, `auto-traits`, `Rc`, `Arc`, `thread-safety`

## Problem Statement

Consider the following code that explores `Send` and `Sync` trait propagation:

```rust
use std::rc::Rc;
use std::sync::Arc;
use std::cell::RefCell;

fn require_send<T: Send>(_: &T) {}
fn require_sync<T: Sync>(_: &T) {}

fn main() {
    let a = Arc::new(42i32);
    let b = Arc::new(RefCell::new(42i32));
    let c = Rc::new(42i32);
    
    require_send(&a);    // Line 1
    require_sync(&a);    // Line 2
    require_send(&b);    // Line 3
    require_sync(&b);    // Line 4
    require_send(&c);    // Line 5
}
```

## Question

Which lines fail to compile?

## Options

- A) Lines 3 and 5 only
- B) Lines 4 and 5 only
- C) Lines 3, 4, and 5
- D) Line 5 only
