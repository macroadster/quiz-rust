# Problem 092: Method Call on Owned vs Borrowed Self — Solution

## Answer

**Correct: B)**

The method `consume` takes `self` by value, which requires ownership of the `Token`. When called as `r.consume()`, the compiler tries to autoderef `r` (which is `&Token`) to get a `Token` value. However, moving out of a shared reference (`&Token`) is not allowed — you can only move out of an *owned* value.

The compiler will attempt autoderef to resolve the method, and it *does* find `consume` on `Token`. But using it would require moving `*r`, which is behind a shared reference. This is a move-out-of-borrow error, not a "method not found" error.

The key distinction is between autoref (adding `&` or `&mut`) and autoderef (removing `&` via `Deref`). Autoref is always safe because it only creates a reference. But autoderef that results in a by-value receiver requires moving the value, which is not always possible.

## Common LLM Mistakes

1. **Thinking autoderef allows moving out of references:** LLMs often assume that since autoderef works for `&self` methods, it should work for `self` methods too. But moving out of a reference is fundamentally different from borrowing through a reference.
2. **Claiming the method is "not found":** The method *is* found — the error is about the inability to move, not about method resolution. The error message refers to moving out of a shared reference.
3. **Assuming implicit cloning:** Rust never implicitly clones values. Unlike C++, there are no implicit copy constructors for non-`Copy` types.

## Key Concepts

- Methods taking `self` by value require ownership of the receiver
- Autoderef can find methods on inner types, but cannot move out of shared references
- Autoref (adding `&`) is always allowed; moving through autoderef is not
- Rust never implicitly clones non-`Copy` types
