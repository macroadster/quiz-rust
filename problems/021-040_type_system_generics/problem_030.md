# Problem 030: Higher-Ranked Trait Bounds — for<'a> Basics

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `HRTB`, `for-lifetime`, `closures`, `higher-ranked-bounds`

## Problem Statement

Compare these two function signatures:

```rust
fn apply_to_ref_a<'a>(f: fn(&'a str) -> &'a str, s: &'a str) -> &'a str {
    f(s)
}

fn apply_to_ref_b(f: fn(&str) -> &str, s: &str) -> &str {
    f(s)
}

fn apply_to_ref_c(f: for<'a> fn(&'a str) -> &'a str, s: &str) -> &str {
    f(s)
}

fn identity(s: &str) -> &str {
    s
}

fn main() {
    let owned = String::from("hello");
    
    let r1 = apply_to_ref_a(identity, &owned);
    println!("{}", r1);
    
    let r2 = apply_to_ref_b(identity, &owned);
    println!("{}", r2);
    
    let r3 = apply_to_ref_c(identity, &owned);
    println!("{}", r3);
}
```

## Question

Do all three calls compile? What is the relationship between signatures `b` and `c`?

## Options

- A) All three compile; `b` and `c` have identical signatures because Rust desugars `b`'s signature to include `for<'a>`
- B) Only `a` and `b` compile; `c` fails because `for<'a>` is not valid on function pointer types
- C) Only `a` and `c` compile; `b` fails because the lifetime elision is ambiguous
- D) All three compile; `b` and `c` are different — `b` uses a specific lifetime while `c` is universally quantified

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
