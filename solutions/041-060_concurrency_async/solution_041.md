# Problem 041: Why &Cell Is !Send, Yet Mutex<Cell> Is Sync — Solution

## Answer

**Correct: A)**

Three independent auto-trait rules collide here. Only Lines B and D fail.

### The rules

1. **`Cell<T>` is `Send` iff `T: Send`, and is never `Sync`.**  
   `Cell` offers unsynchronized interior mutability through `&self`. Sharing a `&Cell<T>` across threads would let two threads `set` the same location with no atomicity — hence the explicit `impl<T: ?Sized> !Sync for Cell<T>`. Moving an *owned* `Cell` to another thread is fine: there is still only one owner.

2. **`&T: Send` if and only if `T: Sync`.**  
   Sending a shared reference means the referent may now be accessed from two threads at once. That is exactly the definition of `Sync`. The std impl is `unsafe impl<T: Sync + ?Sized> Send for &T`.

3. **`Mutex<T>: Send + Sync` if and only if `T: Send` — not `T: Sync`.**  
   The std impls are:
   ```rust
   unsafe impl<T: ?Sized + Send> Send for Mutex<T> {}
   unsafe impl<T: ?Sized + Send> Sync for Mutex<T> {}
   ```
   Locking a `Mutex` on another thread takes *ownership* of the inner `T` for the duration of the guard (and `into_inner` can move `T` out entirely). That requires `T: Send`. It does **not** require `T: Sync`, because the mutex itself serializes access — no two threads ever hold `&T` concurrently. This is why `Mutex` can "upgrade" a `Send + !Sync` type such as `Cell<i32>` into a `Sync` wrapper. It cannot upgrade `Rc<i32>`, because `Rc` is `!Send`.

### Line by line

- **Line A compiles.** `require_send(cell)` moves an owned `Cell<i32>`. `i32: Send`, so `Cell<i32>: Send`.
- **Line B fails.** `require_send(&cell)` demands `&Cell<i32>: Send`, which by rule 2 demands `Cell<i32>: Sync`. `Cell` is explicitly `!Sync`. rustc reports: `` `&Cell<i32>` cannot be sent between threads safely ``.
- **Line C compiles.** `Mutex<Cell<i32>>: Sync` because `Cell<i32>: Send` (rule 3). The mutex is the synchronization; the inner `Cell` need not be `Sync`.
- **Line D fails.** `Mutex<Rc<i32>>: Sync` requires `Rc<i32>: Send`. `Rc` is `!Send` (non-atomic refcount). rustc reports: `` the trait `Send` is not implemented for `Rc<i32>` ``, `` required for `Mutex<Rc<i32>>` to implement `Sync` ``.

So the failing lines are **B and D**.

## Common LLM Mistakes

1. **Choosing B** — The most common expert-level error: assuming `Mutex<T>: Sync` requires `T: Sync`. That would make Line C fail. The bound is inverted from intuition: mutex sync-ness is gated on `T: Send`, because the lock transfers ownership of `T` across threads, not shared references to `T`.
2. **Choosing C** — Confusing `Cell` with `Rc`. Both are `!Sync`, but they diverge on `Send`: `Cell<T>` (like `RefCell<T>`) *is* `Send` when `T: Send`. `Rc<T>` / `Weak<T>` are unconditionally `!Send` because their reference counts are non-atomic.
3. **Choosing D** — Applying "`T: Send` implies `&T: Send`". That implication is false. `&T: Send` is equivalent to `T: Sync`. An owned `Cell` can cross threads; a shared reference to one cannot.
4. **Believing `Mutex` makes any inner type thread-safe** — wrapping `Rc` in `Mutex` does not restore `Send`/`Sync`. Two threads still must not mutate the same non-atomic refcount, and dropping the last `Rc` on a different thread would be unsound.

## Key Concepts

- `Cell<T>: Send` when `T: Send`; `Cell<T>` is never `Sync`
- `&T: Send` ⟺ `T: Sync` (sending a shared reference *is* sharing)
- `&mut T: Send` ⟺ `T: Send` (exclusive access moves with the reference)
- `Mutex<T>: Send + Sync` when `T: Send` — **not** when `T: Sync`
- `Mutex` upgrades `Send + !Sync` types (`Cell`, `RefCell`) into `Sync` wrappers
- `Mutex` cannot upgrade `!Send` types (`Rc`, raw pointers) into `Send`/`Sync`
- `Rc<T>` is unconditionally `!Send` and `!Sync`
