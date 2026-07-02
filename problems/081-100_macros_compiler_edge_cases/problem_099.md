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
