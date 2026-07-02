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
