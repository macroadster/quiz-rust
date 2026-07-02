# Problem 072: Mutable Reference to Shared Reference Invalidation — Solution

## Answer

**Correct: C)**

This does not compile. Even though `raw` is a raw pointer (`*mut i32`), the mutable borrow from `&mut x` is still considered live because `raw` is later used in `*raw = 42`. When `let shared = &x` tries to create a shared reference, the compiler sees the outstanding mutable borrow and rejects it:

```
error[E0502]: cannot borrow `x` as immutable because it is also borrowed as mutable
```

The mutable borrow created by `&mut x` extends until the last use of any value derived from it — in this case, `raw` is used in `*raw = 42`, so the mutable borrow spans past `let shared = &x`.

A common misconception is that converting to a raw pointer "releases" the mutable borrow. It does not — the raw pointer carries the borrow's lifetime for NLL analysis.

## Common LLM Mistakes

1. **Claiming it compiles and is UB:** LLMs often assume that converting to a raw pointer ends the mutable borrow. It doesn't — the borrow checker tracks that `raw` was derived from `&mut x` and keeps the mutable borrow alive until `raw`'s last use.
2. **Claiming it prints `42`:** The program never runs because it doesn't compile.
3. **Thinking raw pointers are invisible to the borrow checker:** Raw pointers are not borrow-checked when *dereferenced*, but the *creation* of a raw pointer from a reference is still tracked by NLL (Non-Lexical Lifetimes).

## Key Concepts

- Converting `&mut T` to `*mut T` does NOT release the mutable borrow
- The borrow checker tracks that raw pointers are derived from borrows
- The mutable borrow lives until the last use of the raw pointer (NLL)
- This is different from the raw pointer's own lack of borrow-checking on dereference
