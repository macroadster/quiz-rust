# Problem 099: Operator Overloading and Ownership Surprises

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `operator-overloading`, `Add-trait`, `ownership`, `move-semantics`

## Problem Statement

A developer overloads the `+` operator for a custom type and is surprised by ownership behavior:

```rust
use std::ops::Add;

#[derive(Debug, Clone)]
struct Point {
    x: f64,
    y: f64,
}

impl Add for Point {
    type Output = Point;
    fn add(self, rhs: Point) -> Point {
        Point {
            x: self.x + rhs.x,
            y: self.y + rhs.y,
        }
    }
}

fn main() {
    let a = Point { x: 1.0, y: 2.0 };
    let b = Point { x: 3.0, y: 4.0 };
    let c = a + b;
    println!("{:?}", c);
    println!("{:?}", a);
}
```

## Question

What happens when this program is compiled?

## Options

- A) Prints `Point { x: 4.0, y: 6.0 }` and `Point { x: 1.0, y: 2.0 }`
- B) Compilation error — `a` is moved by `a + b`, so `println!("{:?}", a)` fails
- C) Prints `Point { x: 4.0, y: 6.0 }` twice
- D) Compilation error — `Add` trait must return `Self`, not `Point`

## Answer

**Correct: B)**

The `Add` trait's `add` method takes `self` by value: `fn add(self, rhs: Rhs) -> Self::Output`. This means the `+` operator *consumes* (moves) both operands. When `a + b` is evaluated:

1. `a` is moved into the `self` parameter of `add`
2. `b` is moved into the `rhs` parameter of `add`
3. Both `a` and `b` are consumed

The subsequent `println!("{:?}", a)` attempts to use `a` after it has been moved, which is a compilation error.

This is a key difference from primitive types like `i32` or `f64`, where `+` copies (since they implement `Copy`). For non-`Copy` types with operator overloading, operators consume their operands. To preserve the operands, you would need to either:
- Implement `Add` for `&Point` (with `impl Add for &Point` or `impl<'a, 'b> Add<&'b Point> for &'a Point`)
- Clone before adding: `let c = a.clone() + b.clone();`

## Common LLM Mistakes

1. **Assuming `+` works like it does for primitives:** For `Copy` types, `+` copies the values. For non-`Copy` types, `+` *moves* the operands because `Add::add` takes `self` by value.
2. **Not realizing operator overloading consumes values:** LLMs often think operator overloading is purely syntactic sugar that doesn't affect ownership. But `a + b` is literally `a.add(b)`, which moves both.
3. **Thinking `#[derive(Clone)]` prevents moves:** Having `Clone` doesn't mean values are automatically cloned. `Clone` gives you the *option* to clone, but the compiler doesn't insert clones automatically.

## Key Concepts

- `Add::add(self, rhs)` takes both operands by value, consuming them
- For non-`Copy` types, `a + b` moves both `a` and `b`
- Primitive types avoid this because they implement `Copy`
- To preserve operands, implement `Add` for references or explicitly clone
