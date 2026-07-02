# Problem 028: Associated Type Bounds in where Clauses

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `associated-types`, `where-clauses`, `type-constraints`, `iterator`

## Problem Statement

Consider this function that constrains an iterator's associated `Item` type:

```rust
use std::fmt::Debug;

fn print_all<I>(iter: I)
where
    I: Iterator,
    I::Item: Debug + PartialOrd,
{
    let mut items: Vec<I::Item> = iter.collect();
    items.sort_by(|a, b| a.partial_cmp(b).unwrap());
    for item in &items {
        println!("{:?}", item);
    }
}

fn sum_if_numeric<I>(iter: I) -> f64
where
    I: Iterator<Item = f64>,
{
    iter.sum()
}

fn main() {
    print_all(vec![3, 1, 2].into_iter());
    
    let result = sum_if_numeric(vec![1.0, 2.0, 3.0].into_iter());
    println!("sum = {}", result);
    
    // Can we call sum_if_numeric with integers?
    let result2 = sum_if_numeric(vec![1, 2, 3].into_iter());
    println!("sum = {}", result2);
}
```

## Question

Does this code compile? If not, where exactly does it fail?

## Options

- A) Compiles and prints all values followed by `sum = 6` and `sum = 6`
- B) Fails to compile: `sort_by` is not available because `I::Item: PartialOrd` is not sufficient for sorting
- C) Fails to compile: `sum_if_numeric(vec![1, 2, 3].into_iter())` fails because `i32` is not `f64`
- D) Fails to compile: `I::Item: Debug + PartialOrd` cannot be combined in a single where clause
