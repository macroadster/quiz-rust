# Problem 073: Struct Field Drop Order

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `drop-order`, `struct-fields`, `destructors`

## Problem Statement

A developer relies on a specific drop order for struct fields.

```rust
struct Noisy(&'static str);

impl Drop for Noisy {
    fn drop(&mut self) {
        print!("{} ", self.0);
    }
}

struct Container {
    first: Noisy,
    second: Noisy,
    third: Noisy,
}

fn main() {
    let _c = Container {
        second: Noisy("B"),
        first: Noisy("A"),
        third: Noisy("C"),
    };
}
```

## Question

What does this program print when `_c` is dropped?

## Options

- A) `A B C` — fields drop in declaration order
- B) `B A C` — fields drop in initialization order
- C) `C B A` — fields drop in reverse declaration order
- D) Unspecified — Rust does not guarantee struct field drop order
