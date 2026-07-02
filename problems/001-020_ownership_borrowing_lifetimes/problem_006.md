# Problem 006: Reborrowing Failure Through a Trait Object

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `reborrowing`, `trait-objects`, `borrow-splitting`, `method-dispatch`

## Problem Statement

Consider this code where borrow splitting interacts with a helper method:

```rust
struct Pair {
    first: String,
    second: String,
}

impl Pair {
    fn first(&self) -> &str {
        &self.first
    }

    fn set_second(&mut self, val: String) {
        self.second = val;
    }
}

fn main() {
    let mut p = Pair {
        first: String::from("hello"),
        second: String::from("world"),
    };

    let f = p.first();
    p.set_second(String::from("rust"));
    println!("{}", f);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compiles and prints `hello`
- B) Compilation error: cannot borrow `p` as mutable because it is also borrowed as immutable
- C) Compiles and prints `world`
- D) Compilation error: `f` is dangling after `set_second` modifies `p`
