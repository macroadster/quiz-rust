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
