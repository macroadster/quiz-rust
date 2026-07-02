# Problem 098: The Never Type (!) and Coercion Rules

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `never-type`, `type-coercion`, `diverging-functions`, `match-arms`

## Problem Statement

A developer uses the never type (`!`) in various contexts to leverage its coercion behavior:

```rust
fn get_value(opt: Option<i32>) -> i32 {
    let val = match opt {
        Some(v) => v,
        None => return 0,
    };
    val * 2
}

fn main() {
    let x: u32 = {
        let cond = true;
        if cond {
            42u32
        } else {
            panic!("unreachable")
        }
    };

    let y: Vec<String> = vec![loop { break; }];
    
    println!("{}", get_value(Some(5)));
    println!("{}", x);
    println!("{}", y.len());
}
```

## Question

What is the output of this program?

## Options

- A) `10`, `42`, `0`
- B) Compilation error — `return 0` in a match arm has type `!` which is incompatible with `i32`
- C) Compilation error — `panic!()` has type `!` which is incompatible with `u32`
- D) Compilation error — `loop { break; }` has type `()`, which is incompatible with `String`
