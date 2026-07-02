# Problem 024: Orphan Rule with Upstream Trait and Local Type — Solution

## Answer

**Correct: B)**

This violates the orphan rule (RFC 2451 / coherence rules). To implement a foreign trait (`Display`) for a type, the *type* must be local. While `Meters` is a local type, `Vec<Meters>` is not considered local — `Vec` is defined in `std::vec`, and the orphan rule considers the *outermost* type constructor. The fact that the type parameter is a local type (`Meters`) does not make `Vec<Meters>` local.

The precise rule is: you can implement a foreign trait for a type only if the type's outermost type constructor is defined in your crate, OR the trait is local. Since both `Display` and `Vec` are foreign, this impl is rejected.

The fix is to use the newtype pattern: `struct MeterList(Vec<Meters>)` and then implement `Display` for `MeterList`.

Note that `Vec<Meters>` does *not* already have a `Display` implementation — `Vec<T>` never implements `Display` in std. So option C is wrong. The issue is purely about the orphan rule, not about conflicting impls.

## Common LLM Mistakes

1. **Choosing A (thinking it compiles):** LLMs often reason that since `Meters` is local and it's "our type inside a Vec," the orphan rule should be satisfied. They miss that the orphan rule checks the outermost type constructor (`Vec`), not the type parameter.
2. **Choosing C (conflicting impl):** LLMs may incorrectly claim `Vec<T>` already implements `Display`. It doesn't — `Vec<T>` implements `Debug` but not `Display` in std.
3. **Misunderstanding the orphan rule's "covered type" concept:** The rule allows `impl ForeignTrait for ForeignType<LocalType>` only when the local type appears before any uncovered type parameter in specific positions (per RFC 2451). `Vec<Meters>` doesn't satisfy this.

## Key Concepts

- The orphan rule prevents implementing foreign traits for foreign types to maintain coherence
- `Vec<LocalType>` is still considered a foreign type because `Vec` is the outermost type constructor
- The newtype pattern (`struct Wrapper(Vec<T>)`) is the standard workaround
- `Vec<T>` implements `Debug` (when `T: Debug`) but not `Display` in the standard library
