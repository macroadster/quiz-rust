# Problem 031: HRTB with Closures and Trait Bounds — Solution

## Answer

**Correct: B)**

The `for<'a>` bound is critical here. With the HRTB `for<'a> Fn(&'a i32) -> i32`, the closure must work for *any* lifetime — including the short-lived local inside `call_with_local`. This is exactly what we need: the closure is called with `&local` where `local` only lives for the duration of the function body.

If we wrote `fn call_with_local<'a, F>(f: F) -> i32 where F: Fn(&'a i32) -> i32` (without HRTB), then `'a` becomes a lifetime parameter of the outer function, chosen by the *caller*. The caller's `'a` would necessarily outlive the function call. But inside the function, `local` has a shorter lifetime that doesn't live as long as `'a`. So `&local` wouldn't satisfy the `&'a i32` requirement — the borrow checker would reject it.

Now, option C is tricky: `Fn(&i32) -> i32` in a where clause *does* desugar to `for<'a> Fn(&'a i32) -> i32` through lifetime elision in trait bounds. So in practice, writing `F: Fn(&i32) -> i32` is equivalent to the HRTB version. However, the question asks about removing `for<'a>` and using a *named* lifetime parameter instead, which is a different thing.

## Common LLM Mistakes

1. **Choosing C and missing the nuance:** `Fn(&i32) -> i32` does desugar to HRTB, but the question is about what happens without HRTB (i.e., with a named outer lifetime). LLMs sometimes say "it's always implied" without understanding when it is and isn't.
2. **Choosing A (auto-inference):** While elision adds `for<'a>` implicitly in some positions, if you *explicitly* name a lifetime parameter `'a` on the function, the compiler uses that named lifetime rather than creating an HRTB.
3. **Not understanding that named lifetimes are caller-chosen:** The critical distinction is that `'a` as a function parameter is existential (caller picks), while `for<'a>` is universal (callee handles any).

## Key Concepts

- `for<'a> Fn(&'a i32)` means the closure works for any lifetime — necessary for local borrows
- A named lifetime parameter `'a` on the function is chosen by the caller, which may be too long for locals
- `Fn(&i32)` in where clauses desugars to `for<'a> Fn(&'a i32)` through elision
- HRTB is essential when the callee creates values and passes references to the closure
