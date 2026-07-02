# Problem 074: Temporary Drop Timing in Match Guards

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `drop-order`, `temporaries`, `scope`, `match-expressions`

## Problem Statement

A developer creates temporaries inside a `let` binding with a complex expression.

```rust
struct Noisy(&'static str);

impl Drop for Noisy {
    fn drop(&mut self) {
        print!("{} ", self.0);
    }
}

impl Noisy {
    fn value(&self) -> i32 {
        match self.0 {
            "first" => 1,
            "second" => 2,
            _ => 0,
        }
    }
}

fn main() {
    let val = Noisy("first").value() + Noisy("second").value();
    print!("sum={} ", val);
}
```

## Question

What is the output of this program?

## Options

- A) `first second sum=3`
- B) `second first sum=3`
- C) `sum=3 second first`
- D) `sum=3 first second`
