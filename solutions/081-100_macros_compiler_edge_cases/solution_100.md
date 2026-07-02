# Problem 100: The Ultimate Rust Quiz — Multiple Subtle Features Combined — Solution

## Answer

**Correct: A)**

Let's trace through each call carefully:

**Call 1: `print_desc(&s)` where `s: String`**
- `&s` produces `&String`
- `print_desc` expects `&dyn Describe`
- The compiler looks for `impl Describe for String` — it exists
- `&String` is coerced to `&dyn Describe` using the `String` impl
- Output: **`String(len=5)`**

**Call 2: `print_desc(&*s)` where `s: String`**
- `*s` dereferences `String` to `str` (via `Deref<Target = str>`)
- `&*s` produces `&str`
- The compiler looks for `impl Describe for str` — it exists
- `&str` is coerced to `&dyn Describe` using the `str` impl
- Output: **`str(len=5)`**

**Call 3: `print_desc(&*boxed)` where `boxed: Box<String>`**
- `*boxed` dereferences `Box<String>` to `String` (via `Deref<Target = String>`)
- `&*boxed` produces `&String`
- The compiler looks for `impl Describe for String` — it exists
- `&String` is coerced to `&dyn Describe` using the `String` impl
- Output: **`String(len=5)`**

The critical insight for Call 3 is that `Box<String>` derefs to `String`, *not* to `str`. Each `*` follows exactly one `Deref` impl. To reach `str`, you would need `&**boxed` (deref `Box` → `String`, then deref `String` → `str`). Furthermore, when coercing to a trait object, the compiler uses the *direct* impl for the referenced type — it does not apply additional deref coercion first.

## Common LLM Mistakes

1. **Thinking `&*boxed` reaches `str`:** `Box<String>` derefs to `String`, not `str`. Each explicit `*` follows one `Deref` impl step. LLMs often collapse the full deref chain (`Box<String>` → `String` → `str`) into a single `*`.
2. **Confusing deref coercion with trait object coercion:** When building `&dyn Describe` from `&String`, the compiler finds `impl Describe for String` directly. It does *not* deref-coerce `String` to `str` first to use the `str` impl. These are different coercion mechanisms.
3. **Assuming `String` always acts as `str` via deref:** While deref coercion *can* convert `&String` to `&str` in some contexts (like function arguments expecting `&str`), trait object coercion uses the most direct impl available.

## Key Concepts

- Trait object coercion uses the direct impl for the referenced type, without additional deref coercion
- `Box<T>` derefs to `T`; explicit `*` follows exactly one `Deref` step
- `String` and `str` are different types with potentially different trait impls
- The deref chain `Box<String>` → `String` → `str` requires two dereferences, not one
