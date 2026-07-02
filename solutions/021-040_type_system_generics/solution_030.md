# Problem 030: Higher-Ranked Trait Bounds — for<'a> Basics — Solution

## Answer

**Correct: A)**

All three calls compile. The key insight is that in `apply_to_ref_b`, the function pointer type `fn(&str) -> &str` is *desugared by the compiler* to `for<'a> fn(&'a str) -> &'a str` — exactly what `apply_to_ref_c` writes explicitly.

For bare function types and `Fn` trait bounds, lifetime parameters that appear only in the function's arguments (and return type via elision) are automatically universally quantified with `for<'a>`. This is because a function pointer must work for *any* lifetime, not just one specific lifetime.

This means `apply_to_ref_b` and `apply_to_ref_c` have *identical* signatures. The `for<'a>` in `c` is just making explicit what `b` already implies.

In contrast, `apply_to_ref_a` names a specific lifetime `'a` that is a parameter of the outer function. This means the caller chooses the lifetime, not the callee. In this code it still works because the caller provides `&owned` with a concrete lifetime that satisfies `'a`.

## Common LLM Mistakes

1. **Choosing D (thinking b and c differ):** LLMs frequently don't realize that lifetime elision in function pointer types produces `for<'a>`. They assume `b` uses a "default" or "inferred" lifetime rather than a higher-ranked one.
2. **Thinking for<'a> is exotic:** LLMs sometimes treat `for<'a>` as unusual syntax that might not work with function pointers. In reality, it's the standard desugaring.
3. **Confusing outer lifetime parameters with HRTB:** `apply_to_ref_a`'s `'a` is an outer lifetime parameter chosen by the caller, which is fundamentally different from the `for<'a>` in `c` where the callee handles any lifetime.

## Key Concepts

- `fn(&str) -> &str` is desugared to `for<'a> fn(&'a str) -> &'a str`
- Higher-ranked lifetimes (`for<'a>`) mean "for any lifetime" — universally quantified
- Named lifetime parameters on the outer function (`'a`) are existentially quantified — chosen by the caller
- HRTB is the default for function pointers and closure trait bounds
