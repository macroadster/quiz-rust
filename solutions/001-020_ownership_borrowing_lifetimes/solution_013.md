# Problem 013: Covariance of Shared References — Solution

## Answer

**Correct: B)**

The code does not compile. The function `choose` requires both `x` and `y` to have the same lifetime `'a`, and returns `&'a str`. When called with `choose(true, &s1, &s2)`, the compiler must unify the lifetimes. Because `&'a str` is covariant in `'a`, the longer lifetime can be *shortened* to match the shorter one. So `'a` becomes the shorter of the two lifetimes — the lifetime of `s2`. The return value `result` therefore has a lifetime tied to `s2`. When `s2` is dropped at the end of the inner block, `result` becomes invalid. The `println!` outside the block tries to use `result` after `s2` is gone, causing a borrow-check error.

The key insight is that covariance *shortens* lifetimes — `&'long str` can be coerced to `&'short str`, not the other way around. So the unified `'a` is the *intersection* (shorter) of the two lifetimes, not the longer one.

## Common LLM Mistakes

1. **Thinking covariance allows using the longer lifetime:** Covariance means a longer lifetime can be used where a shorter one is expected, but the unified lifetime becomes the shorter one. The result cannot outlive the shorter input.
2. **Claiming the compiler knows which branch is taken:** The borrow checker does not evaluate runtime conditions. Even though `cond` is `true`, the compiler must ensure safety for both branches. The return type's lifetime must account for both `x` and `y`.
3. **Thinking different lifetimes cannot unify into `'a`:** They can — that's exactly what covariance enables. The longer lifetime shrinks to match the shorter one.

## Key Concepts

- `&'a T` is covariant in `'a`: a longer lifetime can be shortened to a shorter one
- When two lifetimes are unified, the result is the shorter (more restrictive) lifetime
- The borrow checker does not perform value-dependent analysis on conditions
