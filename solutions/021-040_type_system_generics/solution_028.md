# Problem 028: Associated Type Bounds in where Clauses — Solution

## Answer

**Correct: C)**

The first call `print_all(vec![3, 1, 2].into_iter())` compiles fine — `i32` implements both `Debug` and `PartialOrd`.

The first `sum_if_numeric(vec![1.0, 2.0, 3.0].into_iter())` also compiles — the iterator yields `f64`, matching `Iterator<Item = f64>`.

The second call `sum_if_numeric(vec![1, 2, 3].into_iter())` fails because `vec![1, 2, 3].into_iter()` produces an iterator with `Item = i32`, but the function requires `Item = f64`. Unlike a bound like `Item: Into<f64>`, the constraint `Item = f64` requires an *exact* type match. There is no implicit numeric conversion in Rust.

The error is: "mismatched types — expected `f64`, found `i32`" or more precisely: "the trait bound is not satisfied: expected `f64` but found `i32`" when matching the `Iterator<Item = f64>` constraint.

## Common LLM Mistakes

1. **Choosing A (thinking integers auto-convert to f64):** LLMs trained on languages with implicit numeric conversion (C, Python, JavaScript) may assume `i32` → `f64` happens automatically. Rust has no implicit numeric conversions.
2. **Choosing B (sort_by issue):** `sort_by` with a custom comparator works fine with `PartialOrd`. LLMs sometimes confuse `sort()` (requires `Ord`) with `sort_by()` (accepts a closure).
3. **Confusing `Iterator<Item = f64>` with `Iterator<Item: Into<f64>>`:** The `=` in associated type bounds means exact equality, not compatibility or convertibility.

## Key Concepts

- `Iterator<Item = T>` requires the associated type to be exactly `T`, not a convertible type
- Rust has no implicit numeric conversions — `i32` to `f64` requires explicit `.into()` or `as`
- `sort_by` works with `PartialOrd` via a closure, while `sort()` requires `Ord`
- Associated type equality in where clauses is strict — use trait bounds for flexibility
