# Problem 090: Method Resolution with Autoref and Trait Implementations — Solution

## Answer

**Correct: A)**

This is a subtle aspect of Rust's method resolution algorithm:

1. **`p.greet()`**: `p` is `Person`. The method `greet` takes `&self`, so the compiler autorefs `p` to `&Person` and calls `<Person as Greet>::greet(&p)`. This calls the `impl Greet for Person` impl and returns `"Hello from Person"`.

2. **`r.greet()`**: `r` is `&Person`. The method `greet` takes `&self`. The compiler first checks if `&Person` has a `greet` method directly. It does — `impl Greet for &Person` defines `greet(&self)` where `Self = &Person`, so `&self` means `self: &&Person`. The compiler autorefs `r` to `&&Person` and calls `<&Person as Greet>::greet(&r)`. This returns `"Hello from &Person"`.

The key rule is that method resolution tries the *receiver type first* before trying dereferenced types. For `r: &Person`, the receiver type `&Person` directly implements `Greet`, so that impl is found first, before the compiler would try dereffing to `Person`.

## Common LLM Mistakes

1. **Thinking `r.greet()` calls the `Person` impl:** LLMs often reason that `r` is `&Person`, and since `greet` takes `&self`, the `Person` impl should be called with `r` serving as `&self`. But method resolution checks the receiver type (`&Person`) first, and `&Person` has its own impl.
2. **Confusing autoref with autoderef:** For `r.greet()`, the compiler doesn't deref `&Person` to `Person` first — it checks if `&Person` itself has the method. Autoderef only occurs if the current type doesn't match.
3. **Claiming ambiguity:** Method resolution is well-defined and deterministic. The algorithm tries types in a specific order: `T`, `&T`, `&mut T`, deref chain, etc. There is no ambiguity here.

## Key Concepts

- Method resolution checks the receiver type first before trying deref'd types
- `impl Trait for &T` and `impl Trait for T` are distinct implementations
- When `T = &Person`, `greet(&self)` means `self: &&Person`
- Autoref and autoderef follow a priority order — exact match beats deref
