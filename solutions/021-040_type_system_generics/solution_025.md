# Problem 025: Blanket Impl Conflict — Solution

## Answer

**Correct: C)**

Even though `Vec<i32>` does *not* currently implement `Display` in the standard library, the compiler still rejects this code with a coherence error. The reason is that Rust's coherence rules are *conservative*: they consider that a future version of the standard library *could* add `impl Display for Vec<i32>`, which would then create an actual conflict. To prevent downstream breakage, the compiler rejects the overlap now.

The error message is: "conflicting implementations of trait `Describe` for type `Vec<i32>`" with a note that the blanket impl `impl<T: Display> Describe for T` could potentially apply to `Vec<i32>`.

This is a critical subtlety: Rust's coherence checker doesn't check whether `Vec<i32>: Display` is *currently* true — it checks whether it *could ever* be true given the orphan rules. Since `Display` and `Vec` are both in std, a future std version could add that impl without violating any orphan rules, so the compiler must conservatively reject the overlap.

## Common LLM Mistakes

1. **Choosing A (specialization):** LLMs frequently assume Rust has "specialization" where specific impls override blanket impls. While specialization is an unstable feature, stable Rust does not allow overlapping impls at all.
2. **Choosing D (Vec doesn't impl Display):** LLMs correctly note that `Vec<i32>` doesn't implement `Display`, but incorrectly conclude this means the blanket impl doesn't apply. The coherence check is forward-looking — it considers *potential* future impls.
3. **Misunderstanding coherence conservatism:** LLMs often apply "current state" reasoning rather than Rust's "potential future state" reasoning for coherence.

## Key Concepts

- Rust's coherence rules are conservative — they reject potential future conflicts, not just current ones
- Blanket impls (`impl<T: Bound> Trait for T`) are checked against all types that *could* satisfy the bound
- Specialization (allowing overlapping impls with priority) is an unstable nightly feature
- The standard library could add `Display` for `Vec<T>` in the future, so the compiler must account for this
