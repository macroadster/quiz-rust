# Problem 031: HRTB with Closures and Trait Bounds

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `HRTB`, `closures`, `Fn-traits`, `lifetime-bounds`

## Problem Statement

Consider this code that attempts to store a closure that operates on references:

```rust
fn call_with_local<F>(f: F) -> i32
where
    F: for<'a> Fn(&'a i32) -> i32,
{
    let local = 42;
    f(&local)
}

fn call_with_local_no_hrtb<'a, F>(f: F, val: &'a i32) -> i32
where
    F: Fn(&'a i32) -> i32,
{
    f(val)
}

fn main() {
    // Closure 1: works with HRTB
    let double = |x: &i32| x * 2;
    let result1 = call_with_local(double);
    println!("result1 = {}", result1);
    
    // Closure 2: try without HRTB
    let triple = |x: &i32| x * 3;
    let val = 10;
    let result2 = call_with_local_no_hrtb(triple, &val);
    println!("result2 = {}", result2);
    
    // Can call_with_local_no_hrtb use a local variable?
    // let result3 = call_with_local_no_hrtb(|x: &i32| x * 4, ???);
}
```

## Question

Both calls compile. But why is `for<'a>` critical for `call_with_local` — what would happen if we removed it and tried to pass a reference to a local variable?

## Options

- A) Without `for<'a>`, `call_with_local` would still work because Rust infers the HRTB automatically for closure bounds
- B) Without `for<'a>`, `call_with_local` would fail because the lifetime `'a` would be tied to the caller's scope, but `local` lives only in the callee's scope
- C) `for<'a>` is redundant — `Fn(&i32) -> i32` in a where clause already implies `for<'a>`
- D) Without `for<'a>`, it would still compile but the closure would receive a dangling reference
