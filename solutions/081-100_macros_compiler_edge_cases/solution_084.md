# Problem 084: Derive Macro Field Visibility — Solution

## Answer

**Correct: B)**

The `#[derive(Clone)]` macro generates an `impl Clone for Config` block *inside the same module* where the struct is defined (`inner`). Since the generated code is in the same module, it has full access to all fields, including private ones. The generated `clone()` method copies all fields correctly.

When `main()` calls `c1.clone()`, it's calling the public method from the `Clone` trait — the caller doesn't need access to private fields because the actual field access happens inside the generated impl, which is in the struct's own module.

Similarly, `Debug` can print private fields because its generated impl is also in the same module.

## Common LLM Mistakes

1. **Thinking derive macros respect the caller's visibility:** LLMs often reason that since `main()` can't access `max_retries`, `clone()` shouldn't work. But the derived impl is generated *in the defining module*, not at the call site.
2. **Confusing field visibility with method visibility:** The `clone()` method is public (from the `Clone` trait). The *implementation* of that method accesses private fields, but it's allowed because it's in the same module.
3. **Thinking `#[derive]` itself checks visibility:** The derive macro generates code in the same module as the struct definition, so field visibility is never an issue for derived implementations.

## Key Concepts

- `#[derive]` macros generate impls in the same module as the struct definition
- Private fields are accessible to code in the same module, including derived impls
- The caller of `clone()` doesn't need field access — the method body handles it
- This applies to all derive macros: `Clone`, `Debug`, `PartialEq`, `Hash`, etc.
