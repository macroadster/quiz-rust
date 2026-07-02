# AGENTS.md — Rust Quiz for LLMs

## Purpose

You are taking a **100-question Rust quiz** designed to benchmark your deep understanding of Rust semantics. Problems are deliberately tricky — they target subtle language rules that LLMs commonly get wrong.

## Ground Rules

1. **Do NOT read the `solutions/` directory.** Your answers must come from your own reasoning, not from looking up the answer key. Treat `solutions/` as off-limits until scoring.
2. **Do NOT compile or run the code.** The point is to test your mental model of Rust — not your ability to invoke `rustc`. Do not use `cargo`, `rustc`, the Rust Playground, or any other compiler/interpreter to check your answers.
3. **Do NOT search the web** for answers, documentation, or hints. Rely entirely on your existing knowledge.

## Repository Layout

```
problems/                                  # ✅ READ THESE — the quiz
  001-020_ownership_borrowing_lifetimes/   #   problem_001.md … problem_020.md
  021-040_type_system_generics/            #   problem_021.md … problem_040.md
  041-060_concurrency_async/               #   problem_041.md … problem_060.md
  061-080_unsafe_ffi_patterns/             #   problem_061.md … problem_080.md
  081-100_macros_compiler_edge_cases/       #   problem_081.md … problem_100.md

solutions/                                 # 🚫 DO NOT READ until scoring
  (mirrors problems/ structure with solution_NNN.md files)
```

## How to Take the Quiz

### Step 1 — Set up the answer sheet

Create `answers.md` in the repo root with this exact template:

```markdown
# Rust Quiz — Answer Sheet

**Model:** [your model name/version]
**Date:** [today's date]

| # | Answer | Confidence | Reasoning (1 sentence) |
|---|--------|------------|------------------------|
| 001 | | | |
| 002 | | | |
...
| 100 | | | |
```

- **Answer** column: a single letter — `A`, `B`, `C`, or `D`.
- **Confidence** column: `high`, `medium`, or `low`.
- **Reasoning** column: one sentence explaining *why* you chose that answer. Reference the specific Rust rule or behavior (e.g., "NLL ends the borrow at last use, so the mutable borrow is valid").

### Step 2 — Work through problems sequentially

Read each `problem_NNN.md` from `problems/`. For every problem:

1. Read the code snippet carefully. Pay attention to editions, trait impls, lifetimes, and type annotations.
2. Mentally trace execution or compilation. Ask yourself:
   - Does this compile? What are the borrow lifetimes? What are the types?
   - If it compiles, what is the output? Trace `println!` calls in order.
   - If it doesn't compile, what *specific* rule is violated?
3. Choose exactly **one** option (A/B/C/D).
4. Record your answer, confidence, and one-sentence reasoning in `answers.md`.
5. Move to the next problem. **Do not go back and change answers** after seeing later problems.

### Step 3 — Grade your answers

After completing **all 100 problems**, score yourself:

1. Read each `solutions/*/solution_NNN.md` alongside your answer.
2. Create `results.md` in the repo root with:

```markdown
# Rust Quiz — Results

**Model:** [your model name/version]
**Date:** [today's date]

## Summary

| Metric | Value |
|--------|-------|
| Total Correct | /100 |
| Category 1: Ownership, Borrowing & Lifetimes (001–020) | /20 |
| Category 2: Type System & Generics (021–040) | /20 |
| Category 3: Concurrency & Async (041–060) | /20 |
| Category 4: Unsafe, FFI & Advanced Patterns (061–080) | /20 |
| Category 5: Macros, Compiler Internals & Edge Cases (081–100) | /20 |
| High-confidence accuracy | % |
| Low-confidence accuracy | % |

## Missed Problems

| # | Your Answer | Correct Answer | Why You Got It Wrong |
|---|-------------|----------------|----------------------|
| | | | |
```

For each missed problem, write a brief explanation of what you got wrong and what the correct reasoning is. This is the most valuable part — it reveals systematic blind spots.

## Problem Format Reference

Each problem file contains:

- **Title** with difficulty rating (⭐⭐⭐ to ⭐⭐⭐⭐⭐)
- **Code snippet** in a Rust fenced code block
- **Question** — typically "Does this compile?", "What is the output?", or "What happens?"
- **Four options** (A–D) — exactly one is correct

## Tips for Honest Self-Assessment

- If you are genuinely unsure, mark confidence as `low` — don't guess `high`.
- Watch for distractor options that sound plausible but misstate a Rust rule.
- Many problems hinge on a **single subtle distinction** — read all four options carefully before choosing.
- Common traps: confusing covariance/invariance, forgetting NLL, wrong drop order, assuming `unsafe` always means UB, misunderstanding object safety rules.
