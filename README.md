# 🦀 100 Difficult Rust Problems for LLMs

A curated collection of **100 challenging Rust problems** designed to test and benchmark Large Language Models on deep Rust knowledge. Each problem targets subtle language semantics, tricky edge cases, and advanced features that require genuine understanding — not pattern matching.

## 🎯 Purpose

These problems are specifically designed to:
- **Benchmark LLM Rust competency** beyond surface-level syntax
- **Expose common LLM failure modes** (hallucinated lifetimes, incorrect Send/Sync reasoning, wrong variance, etc.)
- **Test deep understanding** of ownership, borrowing, type theory, concurrency, unsafe, and macros
- **Provide a standardized evaluation set** for comparing models

## 📊 Difficulty Ratings

| Rating | Description |
|--------|-------------|
| ⭐⭐⭐ | Hard — requires solid intermediate Rust knowledge |
| ⭐⭐⭐⭐ | Very Hard — requires advanced understanding of Rust semantics |
| ⭐⭐⭐⭐⭐ | Expert — requires deep compiler/language internals knowledge |

## 📂 Categories

| # | Category | Problems | Focus Areas |
|---|----------|----------|-------------|
| 1 | [Ownership, Borrowing & Lifetimes](problems/001-020_ownership_borrowing_lifetimes/) | 001–020 | Move semantics, NLL, lifetime elision, variance, subtyping |
| 2 | [Type System & Generics](problems/021-040_type_system_generics/) | 021–040 | Trait objects, coherence, associated types, HRTBs, GATs |
| 3 | [Concurrency & Async](problems/041-060_concurrency_async/) | 041–060 | Send/Sync, Pin, Future combinators, executor semantics |
| 4 | [Unsafe, FFI & Advanced Patterns](problems/061-080_unsafe_ffi_patterns/) | 061–080 | Raw pointers, transmute, repr, UB, aliasing, drop order |
| 5 | [Macros, Compiler Internals & Edge Cases](problems/081-100_macros_compiler_edge_cases/) | 081–100 | proc macros, macro_rules tricks, type inference, autoref |

## 📋 Problem Format

Each problem is a standalone Markdown file containing:

```
## Problem Title
**Difficulty:** ⭐⭐⭐⭐
**Category:** Category Name
**Tags:** `tag1`, `tag2`, `tag3`

### Problem Statement
Description of the problem with code snippet.

### Question
What does this code do? / Does this compile? / What is the output?

### Options (if multiple choice)
- A) ...
- B) ...
- C) ...
- D) ...

### Answer
The correct answer with detailed explanation.

### Common LLM Mistakes
Known failure modes and why LLMs get this wrong.

### Key Concepts
Bullet points of the Rust concepts being tested.
```

## 🚀 Quick Start

Browse the [problem index](#problem-index) below, or dive into a category folder.

---

## Problem Index

<!-- INDEX_START -->

### Category 1: Ownership, Borrowing & Lifetimes (001–020)

| # | Problem | Difficulty |
|---|---------|------------|
| 001 | [Partial Move in Destructuring](problems/001-020_ownership_borrowing_lifetimes/problem_001.md) | ⭐⭐⭐⭐ |
| 002 | [Move Semantics in Closures with FnOnce](problems/001-020_ownership_borrowing_lifetimes/problem_002.md) | ⭐⭐⭐⭐ |
| 003 | [Move Through Match Arms with Guard Clauses](problems/001-020_ownership_borrowing_lifetimes/problem_003.md) | ⭐⭐⭐⭐⭐ |
| 004 | [Reborrowing Through &mut References](problems/001-020_ownership_borrowing_lifetimes/problem_004.md) | ⭐⭐⭐⭐ |
| 005 | [Simultaneous Borrows of Distinct Struct Fields](problems/001-020_ownership_borrowing_lifetimes/problem_005.md) | ⭐⭐⭐⭐ |
| 006 | [Reborrowing Failure Through a Trait Object](problems/001-020_ownership_borrowing_lifetimes/problem_006.md) | ⭐⭐⭐⭐⭐ |
| 007 | [NLL — Borrow Ends at Last Use](problems/001-020_ownership_borrowing_lifetimes/problem_007.md) | ⭐⭐⭐⭐ |
| 008 | [Two-Phase Borrowing](problems/001-020_ownership_borrowing_lifetimes/problem_008.md) | ⭐⭐⭐⭐⭐ |
| 009 | [NLL and Conditional Borrow Usage](problems/001-020_ownership_borrowing_lifetimes/problem_009.md) | ⭐⭐⭐⭐ |
| 010 | [Lifetime Elision with Multiple Input References](problems/001-020_ownership_borrowing_lifetimes/problem_010.md) | ⭐⭐⭐⭐ |
| 011 | [Lifetime Elision in Impl Methods](problems/001-020_ownership_borrowing_lifetimes/problem_011.md) | ⭐⭐⭐⭐ |
| 012 | [Lifetime Elision with Struct Impls Returning Inner References](problems/001-020_ownership_borrowing_lifetimes/problem_012.md) | ⭐⭐⭐⭐⭐ |
| 013 | [Covariance of Shared References](problems/001-020_ownership_borrowing_lifetimes/problem_013.md) | ⭐⭐⭐⭐⭐ |
| 014 | [Invariance of &mut T in Lifetimes](problems/001-020_ownership_borrowing_lifetimes/problem_014.md) | ⭐⭐⭐⭐⭐ |
| 015 | [Contravariance of Function Parameters](problems/001-020_ownership_borrowing_lifetimes/problem_015.md) | ⭐⭐⭐⭐⭐ |
| 016 | [Self-Referential Struct — The Pin Problem](problems/001-020_ownership_borrowing_lifetimes/problem_016.md) | ⭐⭐⭐⭐⭐ |
| 017 | [Why Lifetime-Based Self-Referential Structs Don't Work](problems/001-020_ownership_borrowing_lifetimes/problem_017.md) | ⭐⭐⭐⭐⭐ |
| 018 | [Drop Order in Structs and Tuples](problems/001-020_ownership_borrowing_lifetimes/problem_018.md) | ⭐⭐⭐⭐ |
| 019 | [Drop Order Interaction with Borrows](problems/001-020_ownership_borrowing_lifetimes/problem_019.md) | ⭐⭐⭐⭐⭐ |
| 020 | [Lifetime Bounds on Trait Objects](problems/001-020_ownership_borrowing_lifetimes/problem_020.md) | ⭐⭐⭐⭐⭐ |

### Category 2: Type System & Generics (021–040)

| # | Problem | Difficulty |
|---|---------|------------|
| 021 | [Generic Method in Trait Object](problems/021-040_type_system_generics/problem_021.md) | ⭐⭐⭐⭐ |
| 022 | [Self-Returning Method and Object Safety](problems/021-040_type_system_generics/problem_022.md) | ⭐⭐⭐⭐ |
| 023 | [Partial Object Safety with where Self: Sized](problems/021-040_type_system_generics/problem_023.md) | ⭐⭐⭐⭐⭐ |
| 024 | [Orphan Rule with Upstream Trait and Local Type](problems/021-040_type_system_generics/problem_024.md) | ⭐⭐⭐⭐ |
| 025 | [Blanket Impl Conflict](problems/021-040_type_system_generics/problem_025.md) | ⭐⭐⭐⭐ |
| 026 | [Newtype Pattern and Deref Coherence](problems/021-040_type_system_generics/problem_026.md) | ⭐⭐⭐⭐ |
| 027 | [Associated Type vs Generic Parameter — Multiple Implementations](problems/021-040_type_system_generics/problem_027.md) | ⭐⭐⭐⭐ |
| 028 | [Associated Type Bounds in where Clauses](problems/021-040_type_system_generics/problem_028.md) | ⭐⭐⭐⭐ |
| 029 | [Associated Type Shadowing with Multiple Trait Bounds](problems/021-040_type_system_generics/problem_029.md) | ⭐⭐⭐⭐⭐ |
| 030 | [Higher-Ranked Trait Bounds — for<'a> Basics](problems/021-040_type_system_generics/problem_030.md) | ⭐⭐⭐⭐ |
| 031 | [HRTB with Closures and Trait Bounds](problems/021-040_type_system_generics/problem_031.md) | ⭐⭐⭐⭐⭐ |
| 032 | [HRTB Limitation — Returning References from Closures](problems/021-040_type_system_generics/problem_032.md) | ⭐⭐⭐⭐⭐ |
| 033 | [GATs — Lending Iterator Pattern](problems/021-040_type_system_generics/problem_033.md) | ⭐⭐⭐⭐⭐ |
| 034 | [GATs — Type Parameter on Associated Type](problems/021-040_type_system_generics/problem_034.md) | ⭐⭐⭐⭐⭐ |
| 035 | [GATs — Implied Bounds and the where Self: 'a Requirement](problems/021-040_type_system_generics/problem_035.md) | ⭐⭐⭐⭐⭐ |
| 036 | [Send and Sync Propagation with Rc and Arc](problems/021-040_type_system_generics/problem_036.md) | ⭐⭐⭐⭐ |
| 037 | [Unpin and Structural Pinning](problems/021-040_type_system_generics/problem_037.md) | ⭐⭐⭐⭐⭐ |
| 038 | [Deref Coercion Chains and Method Resolution](problems/021-040_type_system_generics/problem_038.md) | ⭐⭐⭐⭐⭐ |
| 039 | [Turbofish and Type Inference Interaction](problems/021-040_type_system_generics/problem_039.md) | ⭐⭐⭐⭐⭐ |
| 040 | [Const Generics Edge Cases](problems/021-040_type_system_generics/problem_040.md) | ⭐⭐⭐⭐⭐ |

### Category 3: Concurrency & Async (041–060)

| # | Problem | Difficulty |
|---|---------|------------|
| 041 | [The Send Bound Surprise with Rc](problems/041-060_concurrency_async/problem_041.md) | ⭐⭐⭐ |
| 042 | [When Does a Type Lose Send?](problems/041-060_concurrency_async/problem_042.md) | ⭐⭐⭐⭐ |
| 043 | [Sync Does Not Imply Send](problems/041-060_concurrency_async/problem_043.md) | ⭐⭐⭐⭐ |
| 044 | [Pinning and Moving — The Fundamental Contract](problems/041-060_concurrency_async/problem_044.md) | ⭐⭐⭐⭐ |
| 045 | [Pin Projection and Structural Pinning](problems/041-060_concurrency_async/problem_045.md) | ⭐⭐⭐⭐⭐ |
| 046 | [Pinning a Future to the Stack](problems/041-060_concurrency_async/problem_046.md) | ⭐⭐⭐⭐ |
| 047 | [Hand-Rolling a Future — Poll Semantics](problems/041-060_concurrency_async/problem_047.md) | ⭐⭐⭐⭐ |
| 048 | [Polling a Future After Completion](problems/041-060_concurrency_async/problem_048.md) | ⭐⭐⭐⭐ |
| 049 | [Waker Cloning and Wake Semantics](problems/041-060_concurrency_async/problem_049.md) | ⭐⭐⭐⭐⭐ |
| 050 | [Borrowing Across .await Points](problems/041-060_concurrency_async/problem_050.md) | ⭐⭐⭐⭐ |
| 051 | [Async Closures and Lifetime Capture](problems/041-060_concurrency_async/problem_051.md) | ⭐⭐⭐⭐⭐ |
| 052 | [Async Trait Methods and Object Safety](problems/041-060_concurrency_async/problem_052.md) | ⭐⭐⭐⭐ |
| 053 | [Mutex Poisoning Semantics](problems/041-060_concurrency_async/problem_053.md) | ⭐⭐⭐⭐ |
| 054 | [Deadlock with Nested Mutex Locks](problems/041-060_concurrency_async/problem_054.md) | ⭐⭐⭐⭐ |
| 055 | [tokio::sync::Mutex vs std::sync::Mutex in Async](problems/041-060_concurrency_async/problem_055.md) | ⭐⭐⭐⭐ |
| 056 | [Relaxed Ordering and Visibility](problems/041-060_concurrency_async/problem_056.md) | ⭐⭐⭐⭐⭐ |
| 057 | [SeqCst vs Acquire/Release — When Does It Matter?](problems/041-060_concurrency_async/problem_057.md) | ⭐⭐⭐⭐⭐ |
| 058 | [mpsc Channel — Send After All Receivers Dropped](problems/041-060_concurrency_async/problem_058.md) | ⭐⭐⭐⭐ |
| 059 | [mpsc::Receiver::recv vs try_recv Semantics](problems/041-060_concurrency_async/problem_059.md) | ⭐⭐⭐⭐ |
| 060 | [Tokio block_on Nesting and Runtime Context](problems/041-060_concurrency_async/problem_060.md) | ⭐⭐⭐⭐⭐ |

### Category 4: Unsafe, FFI & Advanced Patterns (061–080)

| # | Problem | Difficulty |
|---|---------|------------|
| 061 | [Raw Pointer Round-Trip Through Integer](problems/061-080_unsafe_ffi_patterns/problem_061.md) | ⭐⭐⭐⭐ |
| 062 | [Multiple Mutable Raw Pointers](problems/061-080_unsafe_ffi_patterns/problem_062.md) | ⭐⭐⭐⭐ |
| 063 | [Null Pointer Dereference vs Null Pointer Creation](problems/061-080_unsafe_ffi_patterns/problem_063.md) | ⭐⭐⭐⭐ |
| 064 | [transmute Between Different-Sized Types](problems/061-080_unsafe_ffi_patterns/problem_064.md) | ⭐⭐⭐⭐ |
| 065 | [mem::forget and Drop Safety](problems/061-080_unsafe_ffi_patterns/problem_065.md) | ⭐⭐⭐⭐ |
| 066 | [Transmuting Enums and Niche Optimization](problems/061-080_unsafe_ffi_patterns/problem_066.md) | ⭐⭐⭐⭐⭐ |
| 067 | [repr(C) Struct Field Ordering and Padding](problems/061-080_unsafe_ffi_patterns/problem_067.md) | ⭐⭐⭐⭐ |
| 068 | [repr(transparent) and Newtype FFI](problems/061-080_unsafe_ffi_patterns/problem_068.md) | ⭐⭐⭐⭐ |
| 069 | [repr(packed) and Reference Safety](problems/061-080_unsafe_ffi_patterns/problem_069.md) | ⭐⭐⭐⭐⭐ |
| 070 | [Shared Reference to Mutable Data via UnsafeCell](problems/061-080_unsafe_ffi_patterns/problem_070.md) | ⭐⭐⭐⭐⭐ |
| 071 | [Reborrowing Raw Pointers and Stacked Borrows](problems/061-080_unsafe_ffi_patterns/problem_071.md) | ⭐⭐⭐⭐⭐ |
| 072 | [Mutable Reference to Shared Reference Invalidation](problems/061-080_unsafe_ffi_patterns/problem_072.md) | ⭐⭐⭐⭐⭐ |
| 073 | [Struct Field Drop Order](problems/061-080_unsafe_ffi_patterns/problem_073.md) | ⭐⭐⭐⭐ |
| 074 | [Temporary Drop Timing in Match Guards](problems/061-080_unsafe_ffi_patterns/problem_074.md) | ⭐⭐⭐⭐⭐ |
| 075 | [ManuallyDrop and Double-Free](problems/061-080_unsafe_ffi_patterns/problem_075.md) | ⭐⭐⭐⭐ |
| 076 | [CString Use-After-Free Pitfall](problems/061-080_unsafe_ffi_patterns/problem_076.md) | ⭐⭐⭐⭐⭐ |
| 077 | [Unwinding Across extern "C" Boundaries](problems/061-080_unsafe_ffi_patterns/problem_077.md) | ⭐⭐⭐⭐ |
| 078 | [PhantomData and Variance](problems/061-080_unsafe_ffi_patterns/problem_078.md) | ⭐⭐⭐⭐⭐ |
| 079 | [PhantomData and Drop Check](problems/061-080_unsafe_ffi_patterns/problem_079.md) | ⭐⭐⭐⭐⭐ |
| 080 | [Union Types and Unsafe Field Access](problems/061-080_unsafe_ffi_patterns/problem_080.md) | ⭐⭐⭐⭐ |

### Category 5: Macros, Compiler Internals & Edge Cases (081–100)

| # | Problem | Difficulty |
|---|---------|------------|
| 081 | [Macro Hygiene and Variable Capture](problems/081-100_macros_compiler_edge_cases/problem_081.md) | ⭐⭐⭐⭐ |
| 082 | [Push-Down Accumulation in Recursive Macros](problems/081-100_macros_compiler_edge_cases/problem_082.md) | ⭐⭐⭐⭐⭐ |
| 083 | [macro_rules! Matching Ambiguity and Fragment Specifiers](problems/081-100_macros_compiler_edge_cases/problem_083.md) | ⭐⭐⭐⭐⭐ |
| 084 | [Derive Macro Field Visibility](problems/081-100_macros_compiler_edge_cases/problem_084.md) | ⭐⭐⭐⭐ |
| 085 | [Derive Macro Trait Bound Propagation](problems/081-100_macros_compiler_edge_cases/problem_085.md) | ⭐⭐⭐⭐⭐ |
| 086 | [Token Pasting and Identifier Construction in macro_rules!](problems/081-100_macros_compiler_edge_cases/problem_086.md) | ⭐⭐⭐⭐ |
| 087 | [Integer Literal Type Fallback](problems/081-100_macros_compiler_edge_cases/problem_087.md) | ⭐⭐⭐⭐ |
| 088 | [Closure Type Inference Across Branches](problems/081-100_macros_compiler_edge_cases/problem_088.md) | ⭐⭐⭐⭐ |
| 089 | [Type Inference with Turbofish and Collect](problems/081-100_macros_compiler_edge_cases/problem_089.md) | ⭐⭐⭐⭐ |
| 090 | [Method Resolution with Autoref and Trait Implementations](problems/081-100_macros_compiler_edge_cases/problem_090.md) | ⭐⭐⭐⭐⭐ |
| 091 | [Deref Coercion Chain and Method Dispatch](problems/081-100_macros_compiler_edge_cases/problem_091.md) | ⭐⭐⭐⭐ |
| 092 | [Method Call on Owned vs Borrowed Self](problems/081-100_macros_compiler_edge_cases/problem_092.md) | ⭐⭐⭐⭐⭐ |
| 093 | [Match Ergonomics and Binding Modes](problems/081-100_macros_compiler_edge_cases/problem_093.md) | ⭐⭐⭐⭐⭐ |
| 094 | [ref vs & in Patterns — Subtle Difference](problems/081-100_macros_compiler_edge_cases/problem_094.md) | ⭐⭐⭐⭐⭐ |
| 095 | [Exhaustiveness Checking with Integer Ranges and Guards](problems/081-100_macros_compiler_edge_cases/problem_095.md) | ⭐⭐⭐⭐ |
| 096 | [Const Fn Limitations — Mutable References](problems/081-100_macros_compiler_edge_cases/problem_096.md) | ⭐⭐⭐⭐ |
| 097 | [Const Panic and Compile-Time Assertion](problems/081-100_macros_compiler_edge_cases/problem_097.md) | ⭐⭐⭐⭐ |
| 098 | [The Never Type (!) and Coercion Rules](problems/081-100_macros_compiler_edge_cases/problem_098.md) | ⭐⭐⭐⭐⭐ |
| 099 | [Operator Overloading and Ownership Surprises](problems/081-100_macros_compiler_edge_cases/problem_099.md) | ⭐⭐⭐⭐⭐ |
| 100 | [The Ultimate Rust Quiz — Multiple Subtle Features Combined](problems/081-100_macros_compiler_edge_cases/problem_100.md) | ⭐⭐⭐⭐⭐ |

<!-- INDEX_END -->

## 📄 License

MIT License — free to use for benchmarking, research, and education.
