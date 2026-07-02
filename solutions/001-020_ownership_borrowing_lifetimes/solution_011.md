# Problem 011: Lifetime Elision in Impl Methods — Solution

## Answer

**Correct: C)**

The code does **not** compile. Due to lifetime elision rule 3, the method signature `fn get_or_default(&self, input: &str) -> &str` is desugared to `fn get_or_default<'a, 'b>(&'a self, input: &'b str) -> &'a str`. The output lifetime is tied to `&self`, not to `input`. The function body returns `input` in one branch, which is valid only if `'b: 'a` (i.e., `input` lives at least as long as `self`). The function itself compiles because the compiler assumes the caller will satisfy this constraint. In `main`, `config` outlives `input` (which is dropped at the end of the inner block), so the borrow checker cannot guarantee that `result` (which has lifetime `'a`, tied to `config`) is valid — `input` dies before `result` is used. The error is that `input` does not live long enough.

## Common LLM Mistakes

1. **Thinking the method doesn't compile:** The method body itself compiles — the constraint is pushed to the caller. LLMs sometimes check the method body in isolation and conclude it fails there.
2. **Misapplying the elision rule (tying output to `input`):** With `&self`, elision rule 3 ties the output to `self`'s lifetime, not the other input parameter. LLMs that forget rule 3 may think the output could be tied to `input`.
3. **Saying it compiles and prints `hello`:** This would require `input` to outlive `config`, or at least outlive `result`. Since `input` is dropped before `println!`, this fails.

## Key Concepts

- Elision rule 3: with `&self` or `&mut self`, the output lifetime is tied to `self`
- Method bodies compile if the constraints are satisfiable; errors surface at call sites
- The elided lifetime can cause surprising mismatches between what the body returns and what the caller can provide
