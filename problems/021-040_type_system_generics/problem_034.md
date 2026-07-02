# Problem 034: GATs — Type Parameter on Associated Type

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `GATs`, `generic-associated-types`, `type-constructors`, `higher-kinded`

## Problem Statement

Consider this code using GATs with type parameters (not just lifetimes):

```rust
trait Container {
    type Item<T>;
    
    fn wrap<T>(value: T) -> Self::Item<T>;
    fn unwrap_ref<T>(item: &Self::Item<T>) -> &T;
}

struct VecContainer;

impl Container for VecContainer {
    type Item<T> = Vec<T>;
    
    fn wrap<T>(value: T) -> Vec<T> {
        vec![value]
    }
    
    fn unwrap_ref<T>(item: &Vec<T>) -> &T {
        &item[0]
    }
}

struct OptionContainer;

impl Container for OptionContainer {
    type Item<T> = Option<T>;
    
    fn wrap<T>(value: T) -> Option<T> {
        Some(value)
    }
    
    fn unwrap_ref<T>(item: &Option<T>) -> &T {
        item.as_ref().unwrap()
    }
}

fn double_wrap<C: Container>(x: i32) -> C::Item<C::Item<i32>> {
    C::wrap(C::wrap(x))
}

fn main() {
    let nested: Vec<Vec<i32>> = double_wrap::<VecContainer>(42);
    println!("{:?}", nested);
    
    let nested: Option<Option<i32>> = double_wrap::<OptionContainer>(42);
    println!("{:?}", nested);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `[[42]]` then `Some(Some(42))`
- B) Fails to compile: GATs cannot have type parameters, only lifetime parameters
- C) Fails to compile: `C::Item<C::Item<i32>>` is not valid — GATs cannot be nested
- D) Fails to compile: `Container` is not object-safe due to the GAT, and the `wrap`/`unwrap_ref` methods have generic parameters
