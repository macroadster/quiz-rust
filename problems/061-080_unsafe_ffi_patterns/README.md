# Category 4: Unsafe, FFI & Advanced Patterns

20 problems testing knowledge of unsafe Rust, FFI, and low-level patterns.

## Topics Covered

- **Raw pointer operations** — dereferencing, aliasing, provenance (061–063)
- **transmute & mem::forget** — when they're UB vs safe (064–066)
- **repr(C), repr(transparent), repr(packed)** — layout guarantees (067–069)
- **Aliasing & Stacked Borrows** — what's actually UB under Miri (070–072)
- **Drop order gotchas** — fields, temporaries, ManuallyDrop (073–075)
- **FFI safety** — CString pitfalls, extern fn ABI (076–077)
- **PhantomData** — variance markers, drop check (078–079)
- **Union types** — safety rules (080)
