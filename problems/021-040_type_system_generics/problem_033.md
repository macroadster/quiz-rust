# Problem 033: GATs — Lending Iterator Pattern

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `GATs`, `generic-associated-types`, `lending-iterator`, `lifetimes`

## Problem Statement

Consider this code using Generic Associated Types (GATs) to create a "lending iterator":

```rust
trait LendingIterator {
    type Item<'a> where Self: 'a;
    
    fn next(&mut self) -> Option<Self::Item<'_>>;
}

struct WindowIter<'data> {
    data: &'data [i32],
    pos: usize,
    window_size: usize,
}

impl<'data> LendingIterator for WindowIter<'data> {
    type Item<'a> = &'a [i32] where Self: 'a;
    
    fn next(&mut self) -> Option<Self::Item<'_>> {
        if self.pos + self.window_size <= self.data.len() {
            let window = &self.data[self.pos..self.pos + self.window_size];
            self.pos += 1;
            Some(window)
        } else {
            None
        }
    }
}

fn print_windows(data: &[i32]) {
    let mut iter = WindowIter { data, pos: 0, window_size: 3 };
    while let Some(window) = iter.next() {
        println!("{:?}", window);
    }
}

fn main() {
    print_windows(&[1, 2, 3, 4, 5]);
}
```

## Question

Does this code compile and run correctly? What is the output?

## Options

- A) Compiles and prints `[1, 2, 3]`, `[2, 3, 4]`, `[3, 4, 5]`
- B) Fails to compile: GATs require nightly Rust and the `generic_associated_types` feature
- C) Fails to compile: the `where Self: 'a` bound on the associated type is invalid
- D) Fails to compile: `Self::Item<'_>` is not valid syntax in the return type
