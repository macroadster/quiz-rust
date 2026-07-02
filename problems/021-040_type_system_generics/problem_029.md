# Problem 029: Associated Type Shadowing with Multiple Trait Bounds

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `associated-types`, `generic-parameters`, `ambiguity`, `fully-qualified-syntax`

## Problem Statement

Consider this code where two traits have associated types with the same name:

```rust
trait Source {
    type Item;
    fn get(&self) -> Self::Item;
}

trait Sink {
    type Item;
    fn put(&self, item: Self::Item);
}

struct Adapter;

impl Source for Adapter {
    type Item = String;
    fn get(&self) -> String {
        "hello".to_string()
    }
}

impl Sink for Adapter {
    type Item = i32;
    fn put(&self, item: i32) {
        println!("put: {}", item);
    }
}

fn transfer<T>(t: &T)
where
    T: Source + Sink,
{
    let item = t.get();
    t.put(item);
}

fn main() {
    let a = Adapter;
    transfer(&a);
}
```

## Question

Does this code compile? If not, what is the error?

## Options

- A) Compiles and prints `put: hello`
- B) Fails to compile: ambiguous associated type `Item` in the `transfer` function
- C) Fails to compile: `t.put(item)` passes a `String` (from `Source::Item`) but `Sink::Item` expects `i32`
- D) Fails to compile: `Adapter` cannot implement both `Source` and `Sink` with different `Item` types
