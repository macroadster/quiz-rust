# Problem 066: Transmuting Enums and Niche Optimization — Solution

## Answer

**Correct: D)**

`Option<bool>` uses niche optimization: `bool` only has valid values `0` (`false`) and `1` (`true`), so `Option<bool>` uses `2` as the `None` discriminant — wait, actually the exact niche value chosen is an implementation detail. But the critical point is that `Option<bool>` has size 1 (same as `u8`), so the transmute compiles. However, `bool` only has two valid bit patterns: `0` and `1`. The value `2` is **not** a valid `bool`, so the transmute creates an invalid `Option<bool>`. This is immediate undefined behavior — not just when matched on, but at the point of creation.

Even though the transmute compiles (both types are 1 byte), the resulting bit pattern does not correspond to any valid variant of `Option<bool>`, making this instant UB.

## Common LLM Mistakes

1. **Claiming it prints `none`:** LLMs often reason that since `None` needs a niche value and `2` could be that niche, it would print "none". But even if `2` happens to be the niche value the compiler chose for `None`, you cannot rely on this — the niche value is an implementation detail, and creating a `bool` with value `2` is UB regardless.
2. **Claiming it's a compile error (option B):** `u8` and `Option<bool>` are both 1 byte, so the transmute compiles fine. The error is semantic, not syntactic.
3. **Thinking UB only occurs at the `match`:** The UB occurs at the `transmute` call itself. Creating a value with an invalid bit pattern is immediate UB, not deferred until observation.

## Key Concepts

- `bool` has only two valid bit patterns: `0` and `1` — any other value is instant UB
- `Option<bool>` is 1 byte due to niche optimization, but its internal representation is not guaranteed
- `transmute` checks sizes at compile time but does NOT validate bit patterns
- Creating a value of a type with an invalid bit pattern is UB at the point of creation
