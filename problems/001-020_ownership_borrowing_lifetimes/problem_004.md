# Problem 004: Reborrowing Through &mut References

**Difficulty:** ⭐⭐⭐⭐  
**Category:** Ownership, Borrowing & Lifetimes  
**Tags:** `reborrowing`, `mutable-references`, `borrow-checker`

## Problem Statement

Examine the following code that passes `&mut` references through multiple functions:

```rust
fn append_bar(s: &mut String) {
    s.push_str("bar");
}

fn append_baz(s: &mut String) {
    append_bar(s);
    s.push_str("baz");
}

fn main() {
    let mut s = String::from("foo");
    append_baz(&mut s);
    println!("{}", s);
}
```

## Question

Does this code compile? If so, what is the output?

## Options

- A) Compilation error: `s` is already mutably borrowed in `append_baz`, cannot pass to `append_bar`
- B) Compiles and prints `foobarbaz`
- C) Compilation error: cannot create second `&mut` reference to `s` inside `append_baz`
- D) Compilation error: `s` is moved into `append_bar`, cannot use `s` after the call

## Answer

**Correct: B)**

The code compiles and prints `foobarbaz`. The key mechanism here is **reborrowing**. When `append_baz` receives `s: &mut String` and then calls `append_bar(s)`, the compiler does not *move* the mutable reference. Instead, it performs a reborrow: it implicitly creates `&mut *s`, which is a new, shorter-lived mutable reference to the same data. The original reference `s` is temporarily "suspended" during the call to `append_bar`. Once `append_bar` returns, the reborrow ends, and `s` is active again for the `s.push_str("baz")` call. This is different from moving — `&mut T` references are not `Copy`, but the compiler inserts reborrows automatically when passing them to functions.

## Common LLM Mistakes

1. **Treating `&mut` passing as a move:** LLMs often claim that passing a `&mut` reference to another function moves it, making the original reference invalid. Reborrowing is implicit and automatic, but many LLMs don't account for it.
2. **Claiming two `&mut` references exist simultaneously:** The reborrow creates a new `&mut` reference, but the original is suspended (not active) during the callee's execution. There is never more than one *active* `&mut` reference.
3. **Confusing `&mut T` not being `Copy` with not being reborrowable:** While `&mut T` does not implement `Copy`, the compiler still allows passing it to functions via reborrowing.

## Key Concepts

- Reborrowing (`&mut *s`) is automatically inserted by the compiler when passing `&mut` references
- During a reborrow, the original reference is temporarily suspended
- `&mut T` is not `Copy`, but reborrowing allows it to be "passed through" without consuming it
