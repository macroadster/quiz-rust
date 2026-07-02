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

## Answer

**Correct: D)**

Let's analyze each part:

1. **`get_value`**: The match arm `None => return 0` has type `!` (never). `return` is a diverging expression. Since `!` coerces to any type, it unifies with the `i32` from the `Some` arm. This is fine.

2. **`x` assignment**: `panic!()` has type `!`, which coerces to `u32` to match the other branch. This is fine.

3. **`y` assignment**: `loop { break; }` does **not** have type `!`. A `loop` with a `break` that doesn't carry a value has type `()` (unit), not `!`. Only a `loop` without any `break` (or with `break` that is unreachable) has type `!`. Here, `vec![()]` tries to create a `Vec<()>`, but the annotation says `Vec<String>`. Since `()` is not `String`, this is a type mismatch.

The critical distinction is: `loop { break; }` evaluates to `()` (unit), whereas `loop {}` (no break) would have type `!` (never). The `break` causes the loop to produce a value — and `break;` without an expression produces `()`.

## Common LLM Mistakes

1. **Thinking `loop { break; }` has type `!`:** Only loops that *never terminate* have type `!`. A loop with `break` terminates and produces a value. `break;` produces `()`, so the loop expression has type `()`.
2. **Confusing `!` coercion direction:** `!` can coerce *to* any type, but other types cannot coerce to `!`. LLMs sometimes think any expression can be used where `!` is expected.
3. **Not realizing `return` has type `!`:** `return`, `break`, `continue`, `panic!()`, and `loop {}` (without break) all have type `!`. They diverge — they never produce a value to the enclosing expression.

## Key Concepts

- The never type `!` coerces to any type — it is the "bottom" type
- `loop { break; }` has type `()`, not `!` — only infinite loops (without break) are `!`
- `return`, `break`, `continue`, and `panic!()` are diverging expressions with type `!`
- `break expr` produces the value `expr` from the loop; `break;` produces `()`
