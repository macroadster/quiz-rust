# Problem 006: Reborrowing Failure Through a Trait Object — Solution

## Answer

**Correct: B)**

The code does **not** compile. The call `p.first()` borrows `p` immutably (`&self`), and `f` holds a reference tied to `p`'s lifetime. Then `p.set_second(...)` attempts to borrow `p` mutably (`&mut self`). Since `f` is still alive (used in the `println!` after the mutable borrow), the immutable borrow from `p.first()` and the mutable borrow from `p.set_second()` overlap. Unlike direct field access, **method calls borrow the entire `self`**, so the borrow checker cannot split the borrows at the field level. Even though `first()` only reads `self.first` and `set_second()` only writes `self.second`, the compiler sees overlapping borrows of `p` through `&self` and `&mut self`.

## Common LLM Mistakes

1. **Assuming borrow splitting works through methods:** LLMs that know about borrow splitting for direct field access often incorrectly extend this to method calls. The borrow checker cannot see inside method bodies when checking the caller — it only sees the method signature.
2. **Thinking NLL saves this:** NLL ends borrows at their last use, but `f` is used *after* the `set_second` call, so the borrow is still active.
3. **Claiming it compiles because the fields are disjoint:** While the fields *are* disjoint, the compiler cannot know this from the method signatures alone. `&self` borrows the entire `Pair`.

## Key Concepts

- Method calls borrow the entire `self`, not individual fields
- Borrow splitting works only with direct field access, not through methods
- The borrow checker reasons about method signatures, not method bodies, at call sites
