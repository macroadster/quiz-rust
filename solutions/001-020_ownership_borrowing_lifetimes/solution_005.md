# Problem 005: Simultaneous Borrows of Distinct Struct Fields — Solution

## Answer

**Correct: B)**

The code compiles and prints `test` then `[1, 2, 3, 4]`. Rust's borrow checker supports **disjoint borrow splitting** for struct fields. Since `name_ref` borrows `data.name` and `values_ref` mutably borrows `data.values`, and these are distinct fields with no overlap, both borrows can coexist. The borrow checker tracks borrows at the field level within a single function body. This only works because we're directly accessing fields — if we used a method like `data.get_name()` and `data.get_values_mut()`, it would fail because methods borrow the entire `self`.

## Common LLM Mistakes

1. **Treating struct-level borrow conflicts when fields are disjoint:** LLMs often apply the rule "cannot have `&` and `&mut` to the same data" at the struct level, ignoring that the borrow checker tracks individual fields.
2. **Confusing direct field access with method access:** Borrow splitting only works with direct field access. If a method borrows `&self` or `&mut self`, it borrows the entire struct. LLMs sometimes conflate these two situations.
3. **Forgetting NLL's role:** Under NLL, even if there were a conflict, borrows end at their last use. But here, no conflict exists at all due to field-level tracking.

## Key Concepts

- Rust's borrow checker tracks borrows of struct fields independently (borrow splitting)
- Disjoint fields can be borrowed mutably and immutably simultaneously
- Borrow splitting only works with direct field access, not through methods
