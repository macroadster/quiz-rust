# Problem 061: Raw Pointer Round-Trip Through Integer — Solution

## Answer

**Correct: A)**

This program prints `42`. While pointer provenance is a real concern in the Rust Abstract Machine model (and under Strict Provenance proposals via `ptr::with_addr`), the current stable Rust language *does* allow pointer-to-integer-to-pointer round-trips when the original allocation is still live. The cast `usize as *const T` is a valid cast, `x` is still in scope, and the pointer still refers to valid, initialized memory. Under the current Rust reference and all major compilers, this is well-defined.

Note: Under the **Strict Provenance** experimental model (`-Zmiri-strict-provenance`), Miri will flag this as UB because the round-tripped pointer has lost its provenance tag. However, this is not the default, and standard Rust does not yet mandate strict provenance.

## Common LLM Mistakes

1. **Claiming this is always UB due to provenance:** LLMs often over-apply the strict provenance model. Under *default* Miri and current Rust semantics, integer-to-pointer round-trips are allowed when the allocation is live. Strict provenance is an opt-in experimental model.
2. **Claiming the cast `usize as *const i32` is invalid:** This is a perfectly valid `as` cast in Rust. Both directions (`*const T as usize` and `usize as *const T`) are allowed.
3. **Confusing stack invalidation with scope:** `x` is still alive throughout `main`, so the pointer is not dangling.

## Key Concepts

- Pointer-to-integer-to-pointer round-trips are currently defined behavior in Rust when the allocation is live
- Strict Provenance is an experimental opt-in model, not yet the default
- `as` casts between raw pointers and `usize` are always valid at the type level
