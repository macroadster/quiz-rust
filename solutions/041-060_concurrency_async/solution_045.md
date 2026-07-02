# Problem 045: Pin Projection and Structural Pinning — Solution

## Answer

**Correct: B)**

This code is sound. The key concept is **structural vs non-structural pin projection**:

- **Structural pinning** means: if the container is pinned, the field is also considered pinned. The `unpin_field` method does this for `_pin` — it returns `Pin<&mut PhantomPinned>`.
- **Non-structural pinning** means: even if the container is pinned, the field can be accessed without pin guarantees. The `data` method does this — it returns a plain `&mut String`.

This is valid because `String` implements `Unpin`. When doing non-structural pin projection, you're saying "I won't rely on this field's address being stable." Since `String` doesn't care about being moved (it's `Unpin`), returning `&mut String` from a pinned context is safe (though the `unsafe` is needed because the compiler can't verify this automatically).

The rules are: you must choose *either* structural or non-structural pinning for each field, and you must be consistent. Returning `&mut T` (non-structural) for an `Unpin` field is fine.

## Common LLM Mistakes

1. **Choosing A** — LLMs often believe that any mutation through a `Pin` is unsound. But pin projection to `&mut T` for `Unpin` fields is explicitly allowed and is the standard pattern.
2. **Confusing structural and non-structural pinning** — The struct itself is `!Unpin` (due to `PhantomPinned`), but individual fields that are `Unpin` can still be accessed mutably through non-structural projection.
3. **Choosing D** — `PhantomPinned` doesn't prevent field access. It only makes the containing type `!Unpin`, which means `Pin::new()` (the safe version) cannot be used.

## Key Concepts

- Structural pinning: field is pinned when container is pinned → return `Pin<&mut Field>`
- Non-structural pinning: field is not pinned → return `&mut Field` (requires `Field: Unpin` for soundness)
- `PhantomPinned` makes a type `!Unpin` but doesn't restrict field access
- Pin projection requires `unsafe` because the compiler cannot verify the chosen pinning strategy
