# Problem 032: HRTB Limitation — Returning References from Closures — Solution

## Answer

**Correct: A)**

Both functions compile and produce identical output: `Some("hello")`.

In `find_first`, the lifetime `'s` ties the data, the predicate's input, and the return value together. The closure `|s| s.starts_with('h')` is inferred to have type `Fn(&'s String) -> bool` for the specific `'s` of the data slice.

In `find_first_hrtb`, the `for<'a>` on the predicate means the closure must work for *any* lifetime. The closure `|s| s.starts_with('h')` naturally satisfies this because `starts_with` works for any lifetime on `&String`.

In this particular use case, both are equivalent because:
1. The closure doesn't capture anything with lifetime-sensitive references
2. The closure body (`starts_with`) is generic over the input lifetime
3. The data's lifetime is properly connected to the return type via elision

The HRTB version is technically more general — it could accept closures in contexts where the concrete lifetime isn't known. But for this specific call pattern, both work identically.

## Common LLM Mistakes

1. **Choosing D (strictly more flexible):** While HRTB is more general in theory, for this specific code both functions accept the same closures. LLMs may over-state the practical difference.
2. **Choosing B (HRTB conflicts):** LLMs sometimes think `for<'a>` creates a conflict with the concrete lifetime of `data`. It doesn't — `for<'a>` on the predicate is independent of the return type's lifetime.
3. **Not understanding lifetime elision in `find_first_hrtb`:** The return type `Option<&str>` gets its lifetime from `&[String]` through standard elision rules, independent of the HRTB on the predicate.

## Key Concepts

- Named lifetime parameters and HRTB can produce equivalent code in many practical scenarios
- HRTB (`for<'a>`) on a closure parameter doesn't affect other lifetime relationships in the function
- Closures that don't capture references typically satisfy both named and HRTB constraints
- The real difference emerges when storing closures or using them in more complex lifetime scenarios
