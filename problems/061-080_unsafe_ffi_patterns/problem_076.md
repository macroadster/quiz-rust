# Problem 076: CString Use-After-Free Pitfall

**Difficulty:** ⭐⭐⭐⭐⭐  
**Category:** Unsafe, FFI & Advanced Patterns  
**Tags:** `CString`, `FFI`, `dangling-pointer`, `use-after-free`

## Problem Statement

A developer passes a C string to a C function using `CString`.

```rust
use std::ffi::CString;
use std::os::raw::c_char;

extern "C" {
    fn puts(s: *const c_char) -> i32;
}

fn get_greeting() -> *const c_char {
    let c_string = CString::new("Hello, World!").unwrap();
    c_string.as_ptr()
}

fn main() {
    let ptr = get_greeting();
    unsafe {
        puts(ptr);
    }
}
```

## Question

What happens when this program is compiled and run (assuming `puts` is linked from libc)?

## Options

- A) Undefined behavior — `ptr` is a dangling pointer because `c_string` is dropped at the end of `get_greeting`
- B) Compilation error — `CString::as_ptr()` cannot outlive the `CString`
- C) Prints `Hello, World!`
- D) Compilation error — `extern "C"` functions cannot be called from safe code

## Answer

**Correct: A)**

This is a **use-after-free** and is UB. Here's what happens:

1. In `get_greeting()`, a `CString` is created on the heap.
2. `c_string.as_ptr()` returns a `*const c_char` pointing to the `CString`'s internal buffer.
3. At the end of `get_greeting()`, `c_string` is dropped, freeing the heap allocation.
4. The returned `*const c_char` is now a **dangling pointer**.
5. In `main`, `puts(ptr)` dereferences this dangling pointer — UB.

This compiles without error because `*const c_char` is a raw pointer, and raw pointers have no lifetime tracking. The borrow checker cannot see that `ptr` is derived from a temporary `CString`. This is one of the most common FFI bugs in Rust.

The correct approach is either:
- Keep the `CString` alive: `let cs = CString::new("Hello").unwrap(); unsafe { puts(cs.as_ptr()); }`
- Use `CString::into_raw()` (but then you must later call `CString::from_raw()` to free it)

## Common LLM Mistakes

1. **Claiming the borrow checker catches this:** The borrow checker does NOT track raw pointer lifetimes. `as_ptr()` returns `*const c_char`, which carries no lifetime, so the compiler cannot detect the dangling pointer.
2. **Claiming it works because CString is heap-allocated:** Heap allocation doesn't help — the `Drop` impl for `CString` frees the allocation when `c_string` goes out of scope.
3. **Confusing `as_ptr()` with `into_raw()`:** `into_raw()` consumes the `CString` without dropping it (the caller takes ownership of the memory). `as_ptr()` merely borrows, and the `CString` still drops normally.

## Key Concepts

- `CString::as_ptr()` returns a raw pointer that borrows the `CString`'s data
- Raw pointers (`*const T`) have no lifetime — the borrow checker ignores them
- `CString` frees its buffer on drop — the raw pointer becomes dangling
- Use `into_raw()` to transfer ownership of the buffer (caller must free it later)
