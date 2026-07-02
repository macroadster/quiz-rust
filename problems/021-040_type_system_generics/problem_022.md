# Problem 022: Self-Returning Method and Object Safety

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `object-safety`, `trait-objects`, `Self-type`, `sized`

## Problem Statement

Consider the following code using a trait that returns `Self`:

```rust
trait Cloneable {
    fn duplicate(&self) -> Self;
}

struct Wrapper(i32);

impl Cloneable for Wrapper {
    fn duplicate(&self) -> Self {
        Wrapper(self.0)
    }
}

fn make_copy(item: &dyn Cloneable) -> Box<dyn Cloneable> {
    Box::new(item.duplicate())
}

fn main() {
    let w = Wrapper(42);
    let copy = make_copy(&w);
}
```

## Question

Does this code compile? If not, what is the primary reason?

## Options

- A) Compiles successfully
- B) Fails to compile: `Cloneable` is not object-safe because `duplicate` returns `Self`
- C) Fails to compile: cannot box the result of `item.duplicate()` because the concrete type is unknown
- D) Fails to compile: `&Wrapper` cannot be coerced to `&dyn Cloneable`
