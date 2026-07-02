# Problem 063: Null Pointer Dereference vs Null Pointer Creation — Solution

## Answer

**Correct: B)**

This program compiles and runs without any UB. Creating null raw pointers is perfectly safe in Rust — it's *dereferencing* them that is UB. Both `std::ptr::null()` and `0 as *const i32` produce a null pointer. They compare equal because both have address `0`. `size_of_val` on a raw pointer returns the pointer size (8 bytes on 64-bit).

Key distinction: `*const T` and `*mut T` are just addresses. They can hold any value including null. No safety invariant is attached to creating or holding a raw pointer — only to dereferencing one. This differs from references (`&T` / `&mut T`), which must never be null.

## Common LLM Mistakes

1. **Claiming null pointer creation is UB:** LLMs sometimes conflate null pointers with null references. In Rust, `*const T` can be null safely; `&T` cannot. Creating a null raw pointer has no safety implications.
2. **Claiming `p == q` is false due to provenance:** Null pointers created through different methods still compare equal. Pointer comparison for null pointers is well-defined — provenance concerns don't apply here.
3. **Confusing `size_of_val` on pointers with the pointee size:** `size_of_val(&p)` gives the size of the pointer itself, not what it points to.

## Key Concepts

- Creating null raw pointers is safe and well-defined
- Only *dereferencing* null (or dangling) raw pointers is UB
- `std::ptr::null()` and `0 as *const T` produce identical null pointers
- Raw pointers have no safety invariants on creation — only on use
