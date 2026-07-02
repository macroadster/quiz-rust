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

## Answer

**Correct: B)**

`Pin::into_inner` has the signature `fn into_inner(pin: Pin<Ptr>) -> Ptr where Ptr::Target: Unpin`. This means it requires the pointed-to type to implement `Unpin`.

- **`Movable`:** Contains only `String`, which is `Unpin`. Since all fields are `Unpin`, `Movable` auto-implements `Unpin`. Therefore `Pin::into_inner` works, and we can move the value out.

- **`NotMovable`:** Contains `PhantomPinned`, which is `!Unpin`. This causes `NotMovable` to be `!Unpin` (auto traits propagate — if any field is `!Unpin`, the struct is `!Unpin`). Therefore `Pin::into_inner` fails with: "the trait bound `NotMovable: Unpin` is not satisfied."

The purpose of `Pin` is precisely this: for `Unpin` types, `Pin` is essentially a no-op (you can always get the value out). For `!Unpin` types, `Pin` provides a genuine guarantee that the value won't be moved, which is critical for self-referential types and async futures.

## Common LLM Mistakes

1. **Choosing A (both compile):** LLMs may not check the `Unpin` bound on `Pin::into_inner` and assume it always works.
2. **Not understanding `PhantomPinned`:** LLMs may not know that `PhantomPinned` is specifically designed to opt out of `Unpin`, making a type `!Unpin`.
3. **Choosing C (neither compiles):** LLMs may over-generalize and think `Pin` always prevents unwrapping. For `Unpin` types, `Pin` provides no additional guarantees and can always be unwrapped.

## Key Concepts

- `Unpin` is an auto trait — types are `Unpin` by default unless they contain `!Unpin` fields
- `PhantomPinned` is the standard way to make a type `!Unpin`
- `Pin::into_inner` requires `T: Unpin` — it only works for types that are safe to move
- For `Unpin` types, `Pin<P>` is just a wrapper with no additional restrictions
- Most types in Rust are `Unpin` — notable exceptions are self-referential futures and types with `PhantomPinned`
