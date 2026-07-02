# Problem 040: Const Generics Edge Cases

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `const-generics`, `array-types`, `compile-time`, `type-level-computation`

## Problem Statement

Consider this code using const generics:

```rust
fn first_n<const N: usize>(slice: &[i32]) -> [i32; N] {
    let mut arr = [0i32; N];
    arr.copy_from_slice(&slice[..N]);
    arr
}

fn concat_arrays<const A: usize, const B: usize>(
    a: [i32; A],
    b: [i32; B],
) -> [i32; A + B] {
    let mut result = [0i32; A + B];
    result[..A].copy_from_slice(&a);
    result[A..].copy_from_slice(&b);
    result
}

fn main() {
    let data = vec![1, 2, 3, 4, 5];
    let arr: [i32; 3] = first_n(&data);
    println!("{:?}", arr);
    
    let a = [1, 2, 3];
    let b = [4, 5];
    let c = concat_arrays(a, b);
    println!("{:?}", c);
}
```

## Question

Does this code compile on stable Rust?

## Options

- A) Compiles and prints `[1, 2, 3]` then `[1, 2, 3, 4, 5]`
- B) `first_n` compiles but `concat_arrays` fails: const generic expressions like `A + B` are not allowed on stable Rust
- C) Both fail to compile: const generics are still unstable
- D) `concat_arrays` compiles but `first_n` fails: `[0i32; N]` is not valid with a const generic parameter
