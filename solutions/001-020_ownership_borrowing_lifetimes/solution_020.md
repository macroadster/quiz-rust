# Problem 020: Lifetime Bounds on Trait Objects — Solution

## Answer

**Correct: C)**

The code compiles and prints `Wrapper(hello)`. The key is the `+ '_` lifetime bound on the trait object. By default, `Box<dyn Describe>` implies `Box<dyn Describe + 'static>`, which would require all data inside to be `'static`. Since `Wrapper<'a>` contains a `&'a str`, it wouldn't satisfy `'static` unless the reference were `'static`.

The `+ '_` syntax uses lifetime elision to tie the trait object's lifetime bound to the input reference's lifetime. The desugared signature is effectively `fn make_describer<'a>(s: &'a str) -> Box<dyn Describe + 'a>`. This means the returned box is only valid for as long as `s` is valid.

In `main`, `d` is created inside the inner block where `s` is valid. `d.describe()` returns an owned `String` (no references), so `description` owns its data independently. When the inner block ends, `d` and `s` are dropped, but `description` (an owned `String`) survives. The `println!` outside the block works because `description` is fully owned.

## Common LLM Mistakes

1. **Assuming `Box<dyn Trait>` always means `'static`:** Without an explicit lifetime bound, `Box<dyn Trait>` defaults to `'static`. But `+ '_` or `+ 'a` explicitly overrides this. LLMs often ignore or misunderstand the `+ '_` syntax.
2. **Thinking `s` must outlive the `println!`:** The `describe()` method returns an owned `String`, not a reference. `description` is fully owned and independent of `s` and `d`.
3. **Claiming `+ '_` is invalid syntax:** The `+ '_` syntax is valid Rust and is shorthand for "elide the lifetime bound on this trait object," tying it to the input lifetimes per elision rules.

## Key Concepts

- `Box<dyn Trait>` defaults to `Box<dyn Trait + 'static>` in most contexts
- `+ '_` ties the trait object's lifetime bound to the input lifetime via elision
- Owned return values (`String`) are independent of the borrowed data that produced them
