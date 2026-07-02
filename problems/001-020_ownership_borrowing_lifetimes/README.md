# Category 1: Ownership, Borrowing & Lifetimes

20 problems testing deep understanding of Rust's core memory model.

## Topics Covered

- **Move semantics** — partial moves, moves in closures, moves through match (001–003)
- **Reborrowing & borrow splitting** — simultaneous borrows, reborrowing through `&mut` (004–006)
- **Non-Lexical Lifetimes (NLL)** — when borrows end, two-phase borrowing (007–009)
- **Lifetime elision** — complex cases with surprising results (010–012)
- **Variance & subtyping** — covariance, contravariance, invariance of `&mut T` (013–015)
- **Self-referential structs** — why they're hard in Rust (016–017)
- **Drop order** — interaction with borrows and lifetimes (018–019)
- **Lifetime bounds on trait objects** (020)
