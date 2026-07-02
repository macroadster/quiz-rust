# Problem 023: Partial Object Safety with where Self: Sized — Solution

## Answer

**Correct: C)**

The trait `Transform` *is* object-safe because the `clone_box` method has a `where Self: Sized` bound. This bound effectively removes the method from the vtable of `dyn Transform` — since `dyn Transform` is `!Sized`, any method with `where Self: Sized` is simply excluded from dynamic dispatch. The `name` method has no such restriction, so `describe(&r)` works fine.

However, the call `t.clone_box()` in `try_clone` attempts to call `clone_box` on `&dyn Transform`. Since `dyn Transform` does not satisfy `Self: Sized`, this method is not available through the trait object. The compiler reports an error like: "the method `clone_box` exists for reference `&dyn Transform`, but its trait bounds were not satisfied — `dyn Transform: Sized` is not satisfied."

The `describe` function compiles fine because `name` has no `Sized` restriction.

## Common LLM Mistakes

1. **Choosing A (thinking it compiles):** LLMs often don't track that `where Self: Sized` excludes the method from `dyn Trait` dispatch. They see the method exists and assume it can be called.
2. **Choosing B (thinking the trait isn't object-safe):** This is the opposite mistake — `where Self: Sized` actually *enables* object safety by removing the problematic method from the vtable. LLMs confuse "method can't be called on dyn" with "trait can't be made into dyn."
3. **Not realizing `dyn Trait` is `!Sized`:** The key insight is that `dyn Transform` does not implement `Sized`, so any `where Self: Sized` bound automatically excludes that method from dynamic dispatch.

## Key Concepts

- `where Self: Sized` on a trait method excludes it from the vtable, preserving object safety
- `dyn Trait` is always `!Sized`, so methods bounded by `Self: Sized` cannot be called on trait objects
- A trait can be object-safe even if some methods have `Self` in return position or generic parameters, as long as those methods are gated by `where Self: Sized`
- This pattern is used in the standard library (e.g., `Iterator::cloned` has a `Sized` bound)
