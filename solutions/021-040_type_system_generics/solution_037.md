# Problem 037: Unpin and Structural Pinning — Solution

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
