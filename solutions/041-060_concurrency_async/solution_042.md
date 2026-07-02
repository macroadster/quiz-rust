# Problem 042: When Does a Type Lose Send? — Solution

## Answer

**Correct: C)**

`Send` is an auto trait — a struct automatically implements `Send` if all of its fields implement `Send`. `PhantomData<T>` is `Send` if and only if `T` is `Send`. This is by design: `PhantomData<T>` makes the compiler treat the struct *as if* it contains a `T` for the purposes of auto trait computation.

- `i32` is `Send`, so `MyWrapper<i32>` is `Send` — Line A compiles.
- `*mut u8` is **not** `Send` (raw pointers are explicitly `!Send` and `!Sync`), so `MyWrapper<*mut u8>` is not `Send` — Line B fails.
- `Rc<i32>` is not `Send`, so `MyWrapper<Rc<i32>>` is not `Send` — Line C fails.

## Common LLM Mistakes

1. **Choosing D** — A common mistake is thinking raw pointers are `Send`. They are not. Raw pointers `*const T` and `*mut T` are explicitly `!Send` and `!Sync` because the compiler cannot reason about their thread-safety. You must use `unsafe impl Send` to opt in.
2. **Choosing A** — LLMs sometimes forget that `PhantomData<T>` propagates `T`'s auto-trait bounds to the containing struct. Since `MyWrapper` doesn't store a real `T`, they assume it's always `Send`.
3. **Ignoring PhantomData semantics** — `PhantomData<T>` participates fully in auto-trait inference, behaving as if the struct logically owns a `T`.

## Key Concepts

- `PhantomData<T>` inherits `T`'s auto-trait implementations (`Send`, `Sync`, etc.)
- Raw pointers (`*const T`, `*mut T`) are `!Send` and `!Sync` by default
- Auto traits propagate through struct fields — if any field is `!Send`, the struct is `!Send`
