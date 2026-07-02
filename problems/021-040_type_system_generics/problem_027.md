# Problem 027: Associated Type vs Generic Parameter — Multiple Implementations

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Type System & Generics  
**Tags:** `associated-types`, `generic-parameters`, `trait-design`, `type-families`

## Problem Statement

Consider these two trait designs — one using an associated type and one using a generic parameter:

```rust
// Design A: Associated type
trait ParserA {
    type Output;
    fn parse(&self, input: &str) -> Self::Output;
}

// Design B: Generic parameter
trait ParserB<Output> {
    fn parse(&self, input: &str) -> Output;
}

struct JsonParser;

// Can we implement both multiple times?
impl ParserA for JsonParser {
    type Output = String;
    fn parse(&self, input: &str) -> String {
        format!("parsed: {}", input)
    }
}

impl ParserB<String> for JsonParser {
    fn parse(&self, input: &str) -> String {
        format!("parsed as string: {}", input)
    }
}

impl ParserB<Vec<u8>> for JsonParser {
    fn parse(&self, input: &str) -> Vec<u8> {
        input.as_bytes().to_vec()
    }
}

fn main() {
    let jp = JsonParser;
    let s: String = ParserA::parse(&jp, "hello");
    let s2: String = ParserB::<String>::parse(&jp, "hello");
    let b: Vec<u8> = ParserB::<Vec<u8>>::parse(&jp, "hello");
    println!("{}", s);
    println!("{}", s2);
    println!("{:?}", b);
}
```

## Question

Does this code compile? If so, what happens when we try to add a second `impl ParserA` for `JsonParser` with a different `Output` type?

## Options

- A) The code compiles; you could add another `impl ParserA for JsonParser` with `type Output = Vec<u8>`
- B) The code compiles; you cannot add another `impl ParserA for JsonParser` because associated types allow only one impl per type
- C) The code fails to compile because you cannot have two `ParserB` impls for the same type
- D) The code fails to compile because `ParserA::parse` and `ParserB::parse` create an ambiguous method name
