# Problem 100: The Ultimate Rust Quiz — Multiple Subtle Features Combined

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Macros, Compiler Internals & Edge Cases  
**Tags:** `trait-objects`, `autoref`, `deref-coercion`, `closures`, `lifetime-elision`, `move-semantics`

## Problem Statement

This problem combines multiple subtle Rust features — trait method resolution, deref coercion, and trait object coercion — into one tricky scenario:

```rust
trait Describe {
    fn describe(&self) -> String;
}

impl Describe for str {
    fn describe(&self) -> String {
        format!("str(len={})", self.len())
    }
}

impl Describe for String {
    fn describe(&self) -> String {
        format!("String(len={})", self.len())
    }
}

fn print_desc(d: &dyn Describe) {
    println!("{}", d.describe());
}

fn main() {
    let s = String::from("hello");

    // Call 1: pass &String
    print_desc(&s);

    // Call 2: explicitly deref to str
    print_desc(&*s);

    // Call 3: deref a Box<String>
    let boxed: Box<String> = Box::new(String::from("world"));
    print_desc(&*boxed);
}
```

## Question

What is the output of this program?

## Options

- A) `String(len=5)`, `str(len=5)`, `String(len=5)`
- B) `String(len=5)`, `str(len=5)`, `str(len=5)`
- C) `str(len=5)`, `str(len=5)`, `str(len=5)`
- D) `String(len=5)`, `String(len=5)`, `String(len=5)`
