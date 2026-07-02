# Problem 099: Operator Overloading and Ownership Surprises — Solution

## Answer

**Correct: B)**

The `Add` trait's `add` method takes `self` by value: `fn add(self, rhs: Rhs) -> Self::Output`. This means the `+` operator *consumes* (moves) both operands. When `a + b` is evaluated:

1. `a` is moved into the `self` parameter of `add`
2. `b` is moved into the `rhs` parameter of `add`
3. Both `a` and `b` are consumed

The subsequent `println!("{:?}", a)` attempts to use `a` after it has been moved, which is a compilation error.

This is a key difference from primitive types like `i32` or `f64`, where `+` copies (since they implement `Copy`). For non-`Copy` types with operator overloading, operators consume their operands. To preserve the operands, you would need to either:
- Implement `Add` for `&Point` (with `impl Add for &Point` or `impl<'a, 'b> Add<&'b Point> for &'a Point`)
- Clone before adding: `let c = a.clone() + b.clone();`

## Common LLM Mistakes

1. **Assuming `+` works like it does for primitives:** For `Copy` types, `+` copies the values. For non-`Copy` types, `+` *moves* the operands because `Add::add` takes `self` by value.
2. **Not realizing operator overloading consumes values:** LLMs often think operator overloading is purely syntactic sugar that doesn't affect ownership. But `a + b` is literally `a.add(b)`, which moves both.
3. **Thinking `#[derive(Clone)]` prevents moves:** Having `Clone` doesn't mean values are automatically cloned. `Clone` gives you the *option* to clone, but the compiler doesn't insert clones automatically.

## Key Concepts

- `Add::add(self, rhs)` takes both operands by value, consuming them
- For non-`Copy` types, `a + b` moves both `a` and `b`
- Primitive types avoid this because they implement `Copy`
- To preserve operands, implement `Add` for references or explicitly clone
