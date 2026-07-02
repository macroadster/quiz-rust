# Problem 020: Lifetime Bounds on Trait Objects

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `trait-objects`, `lifetime-bounds`, `dyn-Trait`, `default-bounds`

## Problem Statement

Consider this code that returns a trait object from a function:

```rust
trait Describe {
    fn describe(&self) -> String;
}

struct Wrapper<'a> {
    data: &'a str,
}

impl<'a> Describe for Wrapper<'a> {
    fn describe(&self) -> String {
        format!("Wrapper({})", self.data)
    }
}

fn make_describer(s: &str) -> Box<dyn Describe + '_> {
    Box::new(Wrapper { data: s })
}

fn main() {
    let description;
    {
        let s = String::from("hello");
        let d = make_describer(&s);
        description = d.describe();
    }
    println!("{}", description);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: trait objects cannot have lifetime bounds other than `'static`
- B) Compilation error: `s` does not live long enough — the `Box<dyn Describe + '_>` requires `'static`
- C) Compiles and prints `Wrapper(hello)`
- D) Compilation error: cannot use `+ '_` syntax with trait objects
