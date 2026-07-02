# Problem 022: Self-Returning Method and Object Safety — Solution

## Answer

**Correct: B)**

The trait `Cloneable` is not object-safe because it has a method `duplicate` that returns `Self`. When a trait is used as a trait object (`dyn Cloneable`), the concrete type behind the trait object is erased. A method returning `Self` would need to return a value of the *concrete* type, but through a trait object, the compiler doesn't know what concrete type `Self` is or what its size is.

The compiler rejects `dyn Cloneable` as a type entirely, so the error occurs at the function signature `fn make_copy(item: &dyn Cloneable)`. The error message is: "the trait `Cloneable` cannot be made into an object because method `duplicate` has `Self` as a return type."

This is precisely why the standard library's `Clone` trait is not object-safe — `fn clone(&self) -> Self` prevents `dyn Clone`. The workaround is to return `Box<dyn Cloneable>` from the method itself, or to add `where Self: Sized` to the method to exclude it from dynamic dispatch.

## Common LLM Mistakes

1. **Choosing C instead of B:** LLMs often get the symptom right (can't know the concrete type) but misidentify the stage — the error is at trait object formation, not at the `Box::new()` call. The trait object `dyn Cloneable` is simply not allowed to exist.
2. **Thinking `&self` receiver makes it object-safe:** LLMs sometimes reason that since the method takes `&self` (not `self`), it should be fine. But the return type `Self` is the problem, not the receiver.
3. **Confusing with Clone:** LLMs may recall that `Clone` isn't object-safe but not correctly apply the same rule to this custom trait.

## Key Concepts

- Methods that return `Self` make a trait non-object-safe because the concrete return type is erased
- Object safety is checked when forming the trait object type, not at the call site
- The standard `Clone` trait has the same limitation — this is why `dyn Clone` is not valid
- Workaround: return `Box<dyn Trait>` instead, or add `where Self: Sized` to opt out of dynamic dispatch
