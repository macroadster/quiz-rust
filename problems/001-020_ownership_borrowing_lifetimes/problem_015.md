# Problem 015: Contravariance of Function Parameters

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `variance`, `contravariance`, `function-pointers`, `higher-order-functions`

## Problem Statement

Consider this code involving function pointer subtyping:

```rust
fn print_static(s: &'static str) {
    println!("{}", s);
}

fn apply(f: fn(&str), s: &str) {
    f(s);
}

fn main() {
    let s = String::from("hello");
    apply(print_static, &s);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: `print_static` expects `&'static str`, but `&s` is not `'static`
- B) Compiles and prints `hello`
- C) Compilation error: cannot coerce `fn(&'static str)` to `fn(&str)` — function pointer types are invariant
- D) Compilation error: `s` must be `'static` to be passed to `apply`
