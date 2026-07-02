# Problem 040: Const Generics Edge Cases

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `const-generics`, `array-types`, `compile-time`, `type-level-computation`

## Problem Statement

Consider this code using const generics:

```rust
fn first_n<const N: usize>(slice: &[i32]) -> [i32; N] {
    let mut arr = [0i32; N];
    arr.copy_from_slice(&slice[..N]);
    arr
}

fn concat_arrays<const A: usize, const B: usize>(
    a: [i32; A],
    b: [i32; B],
) -> [i32; A + B] {
    let mut result = [0i32; A + B];
    result[..A].copy_from_slice(&a);
    result[A..].copy_from_slice(&b);
    result
}

fn main() {
    let data = vec![1, 2, 3, 4, 5];
    let arr: [i32; 3] = first_n(&data);
    println!("{:?}", arr);
    
    let a = [1, 2, 3];
    let b = [4, 5];
    let c = concat_arrays(a, b);
    println!("{:?}", c);
}
```

## Question

Does this code compile on stable Rust?

## Options

- A) Compiles and prints `[1, 2, 3]` then `[1, 2, 3, 4, 5]`
- B) `first_n` compiles but `concat_arrays` fails: const generic expressions like `A + B` are not allowed on stable Rust
- C) Both fail to compile: const generics are still unstable
- D) `concat_arrays` compiles but `first_n` fails: `[0i32; N]` is not valid with a const generic parameter

## Answer

**Correct: B)**

`first_n` compiles fine on stable Rust. Const generics with simple `const N: usize` parameters have been stable since Rust 1.51. Using `N` in array types (`[i32; N]`) and array initialization (`[0i32; N]`) is allowed.

However, `concat_arrays` fails because `A + B` is a *const generic expression* — an arithmetic operation on const generic parameters. This requires the `generic_const_exprs` feature, which is still unstable (nightly-only).

On stable Rust, you cannot use:
- `[i32; A + B]` — arithmetic on const generics in type position
- `[0i32; A + B]` — arithmetic on const generics in expression position (for array initialization)
- Any computation involving const generic parameters (e.g., `A * 2`, `A - 1`, `A.min(B)`)

The compiler reports: "generic parameters may not be used in const operations" and suggests adding `#![feature(generic_const_exprs)]`.

The only thing you can do with const generic parameters on stable is use them *directly* as values — `N` is fine, but `N + 1` is not.

## Common LLM Mistakes

1. **Choosing A (thinking both compile):** LLMs frequently generate code with `A + B` in const generic positions, not realizing this requires nightly. It's one of the most common const generics mistakes.
2. **Choosing C (thinking all const generics are unstable):** Basic const generics (`const N: usize`) have been stable since Rust 1.51. Only *expressions* on const generics are unstable.
3. **Choosing D (thinking `[0i32; N]` is invalid):** `[expr; N]` where `N` is a simple const generic parameter works fine on stable. The restriction is on *computed* expressions like `A + B`.

## Key Concepts

- Basic const generics (`const N: usize`) are stable since Rust 1.51
- Const generic *expressions* (`A + B`, `N * 2`) require `#![feature(generic_const_exprs)]` (nightly)
- You can use const generic parameters directly in types and expressions, but cannot perform arithmetic on them
- `[T; N]` and `[value; N]` work with const generics on stable
- The workaround on stable is to add a third const parameter: `const C: usize` and assert `C == A + B` at runtime
