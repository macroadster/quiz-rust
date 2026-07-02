# Problem 037: Unpin and Structural Pinning

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `Unpin`, `Pin`, `auto-traits`, `structural-pinning`, `async`

## Problem Statement

Consider this code exploring the `Unpin` auto trait:

```rust
use std::pin::Pin;
use std::marker::PhantomPinned;

struct Movable {
    data: String,
}

struct NotMovable {
    data: String,
    _pin: PhantomPinned,
}

fn take_by_value<T>(x: T) {
    drop(x);
}

fn try_move_pinned_movable(x: Pin<Box<Movable>>) {
    // Can we get the inner value out?
    let inner: Movable = *Pin::into_inner(x);
    take_by_value(inner);
}

fn try_move_pinned_not_movable(x: Pin<Box<NotMovable>>) {
    let inner: NotMovable = *Pin::into_inner(x);
    take_by_value(inner);
}

fn main() {
    let m = Box::pin(Movable { data: "hello".into() });
    try_move_pinned_movable(m);
    
    let nm = Box::pin(NotMovable { data: "hello".into(), _pin: PhantomPinned });
    try_move_pinned_not_movable(nm);
}
```

## Question

Does this code compile? If not, which function fails and why?

## Options

- A) Both functions compile — `Pin::into_inner` always unwraps `Pin<Box<T>>`
- B) Only `try_move_pinned_movable` compiles — `Pin::into_inner` requires `T: Unpin`, and `NotMovable` is `!Unpin` due to `PhantomPinned`
- C) Neither function compiles — you can never use `Pin::into_inner` with `Pin<Box<T>>`
- D) Only `try_move_pinned_not_movable` compiles — `PhantomPinned` makes the type safely movable after pinning
