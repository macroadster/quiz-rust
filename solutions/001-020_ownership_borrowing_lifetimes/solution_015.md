# Problem 015: Contravariance of Function Parameters — Solution

## Answer

**Correct: B)**

The code compiles and prints `hello`. This works because of **contravariance** of function parameters. The function type `fn(T)` is **contravariant** in `T`. For lifetime subtyping, `'static: 'a` (static outlives any lifetime `'a`), which means `&'static str` is a subtype of `&'a str`. By contravariance, `fn(&'a str)` is a subtype of `fn(&'static str)` — the direction flips!

So `fn(&'static str)` (which is `print_static`) is a **supertype** of `fn(&'a str)`, and since a subtype can be used where a supertype is expected... wait, it's the reverse: `fn(&str)` (with a shorter lifetime) is a *subtype* of `fn(&'static str)`, meaning `fn(&'static str)` can be coerced *to* `fn(&str)`. A function that can handle `&'static str` can certainly handle any `&'a str` where `'a` is shorter — because `print_static` only needs to read the reference, and a `'static` reference is *more restrictive* in what it accepts. A function accepting `&'static str` can be safely used where `fn(&'a str)` is expected, because any `&'a str` can be handled by code that only needs the data during the call.

Actually, the correct reasoning: `fn(&'static str)` can coerce to `fn(&'a str)` for any `'a`. A function that only requires `'static` arguments is *more* permissive to call when given shorter-lived arguments, because it promises not to store the reference beyond the call.

## Common LLM Mistakes

1. **Claiming the coercion is invalid:** LLMs often think `fn(&'static str)` cannot be used as `fn(&str)` because the parameter types differ. They miss contravariance.
2. **Getting the variance direction backwards:** Contravariance is confusing. LLMs may incorrectly claim that function parameters are covariant or invariant.
3. **Conflating function pointers with closures:** Function pointers (`fn(T)`) have well-defined variance rules. Closures and `Fn` trait objects have different (more complex) variance behavior.

## Key Concepts

- `fn(T)` is contravariant in `T` — subtyping of the parameter type is reversed
- `fn(&'static str)` can be used where `fn(&'a str)` is expected for any `'a`
- Contravariance is sound because a function that handles a more restrictive input can safely handle a less restrictive one
