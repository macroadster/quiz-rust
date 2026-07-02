# Problem 023: Partial Object Safety with where Self: Sized

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `object-safety`, `trait-objects`, `where-Self-Sized`, `selective-dispatch`

## Problem Statement

Consider this code that uses `where Self: Sized` to selectively exclude methods from trait objects:

```rust
trait Transform {
    fn name(&self) -> &str;

    fn clone_box(&self) -> Box<dyn Transform>
    where
        Self: Sized + Clone,
    {
        Box::new(self.clone())
    }
}

#[derive(Clone)]
struct Rot13;

impl Transform for Rot13 {
    fn name(&self) -> &str {
        "rot13"
    }
}

fn describe(t: &dyn Transform) {
    println!("Transform: {}", t.name());
}

fn try_clone(t: &dyn Transform) {
    let _copy = t.clone_box();
}

fn main() {
    let r = Rot13;
    describe(&r);
    try_clone(&r);
}
```

## Question

What happens when this code is compiled?

## Options

- A) Compiles and runs, printing `Transform: rot13`
- B) Fails to compile: `Transform` is not object-safe due to `clone_box` returning `Box<dyn Transform>`
- C) Fails to compile: `clone_box` cannot be called on `&dyn Transform` because it has a `where Self: Sized` bound
- D) Fails to compile: `Rot13` doesn't implement `clone_box` explicitly so the default implementation can't be used through `dyn Transform`
