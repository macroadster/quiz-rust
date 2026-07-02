# Problem 009: NLL and Conditional Borrow Usage — Solution

## Answer

**Correct: A)**

The code compiles and prints `hello` then `hello world`. Under NLL, the borrow checker performs flow-sensitive analysis. The reference `r` is assigned inside the `if` block and its last use (`println!("{}", r)`) is also inside that block. After the `if` block ends, `r` is never used again — so the borrow on `data` through `r` has ended. The mutable borrow for `data.push_str(" world")` is then valid. Even though `r` is declared before the `if` block and technically still "in scope," NLL tracks liveness, not scope. Since `r` has no uses after the `if` block, its borrow does not extend beyond the block. Note that `r` is declared but only conditionally assigned, and since it's never used outside the `if`, the compiler does not require it to be initialized in all paths.

## Common LLM Mistakes

1. **Applying lexical lifetime rules:** LLMs may think `r` is live until end of `main` because it was declared in `main`'s scope, leading them to claim a borrow conflict. NLL uses liveness, not lexical scope.
2. **Claiming `r` might be uninitialized:** Since `r` is never used outside the `if` block, the compiler doesn't flag it as potentially uninitialized. If `r` were used after the `if` block, *then* it would be an error.
3. **Confusing "declared" with "live":** A variable being in scope does not mean its borrow is active. NLL only considers a borrow active from its creation to its last use.

## Key Concepts

- NLL uses flow-sensitive liveness analysis, not lexical scopes
- A borrow is only active from creation to last use
- Uninitialized variables are only errors if they are used on paths where they may not be initialized
