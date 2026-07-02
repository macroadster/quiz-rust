# Problem 036: Send and Sync Propagation with Rc and Arc — Solution

## Answer

**Correct: C)**

Let's analyze each line:

- **Line 1 (`require_send(&a)`):** `Arc<i32>` is `Send` when `i32: Send + Sync`. `i32` implements both, so `Arc<i32>: Send`. ✅
- **Line 2 (`require_sync(&a)`):** `Arc<i32>` is `Sync` when `i32: Send + Sync`. `i32` implements both, so `Arc<i32>: Sync`. ✅
- **Line 3 (`require_send(&b)`):** `Arc<T>: Send` requires `T: Send + Sync`. `RefCell<i32>` is `Send` (because `i32: Send`), but `RefCell<i32>` is `!Sync` (RefCell is never Sync). Since `RefCell<i32>` doesn't satisfy the `Sync` requirement, `Arc<RefCell<i32>>` is `!Send`. ❌
- **Line 4 (`require_sync(&b)`):** `Arc<T>: Sync` requires `T: Send + Sync`. Since `RefCell<i32>: !Sync`, `Arc<RefCell<i32>>` is also `!Sync`. ❌
- **Line 5 (`require_send(&c)`):** `Rc<T>` is never `Send`, regardless of `T`. This is because `Rc` uses non-atomic reference counting, which is fundamentally not thread-safe. ❌

Lines 1 and 2 pass. Lines 3, 4, and 5 all fail to compile.

## Common LLM Mistakes

1. **Thinking `Arc<RefCell<T>>` is `Send`:** LLMs often forget that `Arc<T>: Send` requires BOTH `T: Send` AND `T: Sync`. Since `RefCell` is `!Sync`, wrapping it in `Arc` does NOT make it `Send`.
2. **Confusing `Rc` vs `Arc`:** `Rc` is *never* `Send` or `Sync`. `Arc` is `Send`/`Sync` only when `T: Send + Sync`.
3. **Getting line 3 wrong:** This is the subtlest case — `RefCell<i32>` IS `Send`, but `Arc` requires `T: Send + Sync` for `Arc<T>: Send`.

## Key Concepts

- `Arc<T>: Send` requires `T: Send + Sync` (both!)
- `Arc<T>: Sync` requires `T: Send + Sync` (both!)
- `RefCell<T>: Send` when `T: Send`, but `RefCell<T>` is NEVER `Sync`
- `Rc<T>` is never `Send` and never `Sync`, regardless of `T`
- Auto traits propagate structurally — a type is Send/Sync only if all its components satisfy the bounds
