# Problem 038: Deref Coercion Chains and Method Resolution

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `Deref`, `method-resolution`, `auto-deref`, `coercion-chain`, `inherent-methods`

## Problem Statement

Consider this code with a chain of `Deref` implementations and method name conflicts:

```rust
use std::ops::Deref;

struct A;
struct B;
struct C;

impl A {
    fn hello(&self) -> &str { "A" }
}

impl B {
    fn hello(&self) -> &str { "B" }
}

impl C {
    fn hello(&self) -> &str { "C" }
}

impl Deref for C {
    type Target = B;
    fn deref(&self) -> &B { &B }
}

impl Deref for B {
    type Target = A;
    fn deref(&self) -> &A { &A }
}

fn main() {
    let c = C;
    println!("{}", c.hello());
    
    let b: &B = &c;
    println!("{}", b.hello());
    
    let a: &A = &c;
    println!("{}", a.hello());
}
```

## Question

What is the output of this program?

## Options

- A) `C`, `B`, `A`
- B) `A`, `A`, `A` — deref coercion always goes to the deepest target
- C) `C`, `B`, `A` — method resolution prefers the type's own methods first
- D) Fails to compile: ambiguous method `hello` due to deref chain

## Answer

**Correct: C)**

The output is:
```
C
B
A
```

Rust's method resolution follows a specific order:
1. **Direct inherent methods** on the receiver type (highest priority)
2. **Methods found through auto-deref** (one level of `Deref`)
3. **Methods found through further auto-deref** (two levels, three levels, etc.)
4. **Trait methods** (lowest priority for inherent-method conflicts)

For `c.hello()`: `c` is of type `C`. Rust first checks if `C` has an inherent method `hello` — it does! So it calls `C::hello`, printing `"C"`. It never even considers `B::hello` or `A::hello`.

For `b.hello()`: `b` is of type `&B`. Rust checks if `B` has an inherent method `hello` — it does! So it calls `B::hello`, printing `"B"`.

For `a.hello()`: `a` is of type `&A`. Rust checks if `A` has `hello` — it does! Calls `A::hello`, printing `"A"`.

The coercions `let b: &B = &c` and `let a: &A = &c` work through the deref chain: `C → B → A`. But once coerced, the variable has the target type and method resolution starts fresh from that type.

## Common LLM Mistakes

1. **Choosing B (deepest deref wins):** LLMs sometimes think deref always resolves to the most deeply nested type. In fact, inherent methods at each level take priority — deref is only explored when no method is found at the current level.
2. **Choosing D (ambiguity):** There's no ambiguity — method resolution has a clear priority order. Each type's inherent methods shadow those found through deref.
3. **Misunderstanding auto-deref during method calls:** Auto-deref adds `*` operators step by step. At each step, the compiler checks for inherent methods. If found, it stops. If not, it derefs again.

## Key Concepts

- Method resolution tries inherent methods on the current type *before* following `Deref`
- Auto-deref is a sequence: `T`, `*T`, `**T`, etc. — each step checks for methods
- Inherent methods shadow methods found through deref
- Explicit type coercion via `let b: &B = &c` uses the deref chain but produces a new reference type
- This is analogous to how `String::len()` calls `String`'s own method, while `String.is_empty()` would auto-deref to `str::is_empty()` if `String` didn't have one
