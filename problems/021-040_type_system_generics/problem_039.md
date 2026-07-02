# Problem 039: Turbofish and Type Inference Interaction

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `turbofish`, `type-inference`, `parse`, `collect`, `method-chains`

## Problem Statement

Consider the following code that explores turbofish syntax and type inference:

```rust
fn main() {
    // Case 1: turbofish on parse
    let x = "42".parse::<i32>().unwrap();
    println!("x = {}", x);
    
    // Case 2: type annotation instead of turbofish
    let y: i32 = "42".parse().unwrap();
    println!("y = {}", y);
    
    // Case 3: turbofish on collect
    let v = (0..5).collect::<Vec<_>>();
    println!("{:?}", v);
    
    // Case 4: turbofish on a comparison expression
    let result = (0..5).collect::<Vec<i32>>() == vec![0, 1, 2, 3, 4];
    println!("{}", result);
    
    // Case 5: nested turbofish 
    let s = <String as From<&str>>::from("hello");
    println!("{}", s);
    
    // Case 6: can we turbofish a closure call?
    let f = |x: &str| x.parse::<i32>().unwrap();
    let z = f("99");
    println!("z = {}", z);
}
```

## Question

Do all six cases compile? What is the full output?

## Options

- A) All compile. Output: `x = 42`, `y = 42`, `[0, 1, 2, 3, 4]`, `true`, `hello`, `z = 99`
- B) Case 4 fails: cannot compare `Vec<i32>` with `==` after `collect`
- C) Case 5 fails: `<String as From<&str>>::from` is not valid turbofish syntax
- D) Case 6 fails: cannot use turbofish inside a closure body
