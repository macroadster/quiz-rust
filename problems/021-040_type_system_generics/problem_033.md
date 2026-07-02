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

## Answer

**Correct: A)**

This code compiles and runs correctly on stable Rust (GATs were stabilized in Rust 1.65). The output is:
```
[1, 2, 3]
[2, 3, 4]
[3, 4, 5]
```

The `LendingIterator` trait uses a GAT `type Item<'a>` where the associated type is parameterized by a lifetime. This allows each call to `next()` to return a borrow tied to `&mut self` — the `'_` in `Self::Item<'_>` is inferred to be the lifetime of the `&mut self` borrow.

The `where Self: 'a` bound is *required* by the compiler — it ensures that the iterator itself outlives the lifetime `'a` of the item, preventing dangling references. Without this bound, the compiler would reject the GAT definition.

In the `WindowIter` implementation, `Item<'a> = &'a [i32]` means each window is a slice borrowed for the lifetime `'a`. Since the underlying data lives as long as `'data`, and the `where Self: 'a` bound ensures `WindowIter<'data>: 'a`, the borrows are sound.

## Common LLM Mistakes

1. **Choosing B (nightly required):** GATs were stabilized in Rust 1.65 (November 2022). LLMs with training data from before this date may incorrectly say they require nightly.
2. **Not understanding `where Self: 'a`:** LLMs may think this bound is optional or unusual. It's actually required by the compiler for GATs — without it, you get an error asking you to add it.
3. **Confusing GATs with regular associated types:** LLMs may not track that `type Item<'a>` is fundamentally different from `type Item` — the lifetime parameter allows the item to borrow from `&mut self`.

## Key Concepts

- GATs allow associated types to have their own generic parameters (lifetimes or types)
- The `where Self: 'a` bound is typically required and ensures soundness
- `Self::Item<'_>` uses the anonymous lifetime tied to the `&mut self` borrow
- GATs enable patterns like lending iterators that standard `Iterator` cannot express
- GATs are stable as of Rust 1.65
