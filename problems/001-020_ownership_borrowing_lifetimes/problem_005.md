# Problem 005: Simultaneous Borrows of Distinct Struct Fields

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `borrow-splitting`, `struct-fields`, `disjoint-borrows`

## Problem Statement

Consider this code that attempts to borrow different fields of a struct simultaneously:

```rust
struct Data {
    name: String,
    values: Vec<i32>,
}

fn main() {
    let mut data = Data {
        name: String::from("test"),
        values: vec![1, 2, 3],
    };

    let name_ref = &data.name;
    let values_ref = &mut data.values;

    values_ref.push(4);
    println!("{}", name_ref);
    println!("{:?}", values_ref);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: cannot borrow `data.values` as mutable because `data` is also borrowed as immutable
- B) Compiles and prints `test` then `[1, 2, 3, 4]`
- C) Compilation error: cannot have `&` and `&mut` borrows of `data` at the same time
- D) Compiles and prints `test` then `[1, 2, 3]`
