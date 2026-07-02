# Problem 035: GATs — Implied Bounds and the where Self: 'a Requirement

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `GATs`, `implied-bounds`, `where-clauses`, `lifetime-constraints`

## Problem Statement

Consider this code that defines a GAT without the required bound:

```rust
trait StreamingIterator {
    type Item<'a>;
    
    fn next(&mut self) -> Option<Self::Item<'_>>;
    fn size_hint(&self) -> (usize, Option<usize>);
}

struct Chunks<'data> {
    data: &'data [u8],
    chunk_size: usize,
    pos: usize,
}

impl<'data> StreamingIterator for Chunks<'data> {
    type Item<'a> = &'a [u8];
    
    fn next(&mut self) -> Option<&[u8]> {
        if self.pos < self.data.len() {
            let end = (self.pos + self.chunk_size).min(self.data.len());
            let chunk = &self.data[self.pos..end];
            self.pos = end;
            Some(chunk)
        } else {
            None
        }
    }
    
    fn size_hint(&self) -> (usize, Option<usize>) {
        let remaining = self.data.len().saturating_sub(self.pos);
        let chunks = (remaining + self.chunk_size - 1) / self.chunk_size;
        (chunks, Some(chunks))
    }
}

fn main() {
    let data = vec![1u8, 2, 3, 4, 5, 6, 7];
    let mut iter = Chunks { data: &data, chunk_size: 3, pos: 0 };
    while let Some(chunk) = iter.next() {
        println!("{:?}", chunk);
    }
}
```

## Question

Does this code compile on stable Rust (1.65+)?

## Options

- A) Compiles and prints `[1, 2, 3]`, `[4, 5, 6]`, `[7]`
- B) Fails to compile: `type Item<'a>` in the trait requires `where Self: 'a` bound
- C) Fails to compile: `type Item<'a> = &'a [u8]` in the impl needs `where Self: 'a`
- D) Fails to compile: the lifetime of `&self.data[..]` doesn't match `'a`
