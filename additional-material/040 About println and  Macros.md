# About `println!` and Macros

## What `println!` does?

`println!` expands to code that calls the formatting machinery (`std::fmt`) and then writes the result plus `'\n'` to the standard output stream `stdout` [Rust Documentation](https://doc.rust-lang.org/std/macro.println.html).  
It locks `stdout` on each call to prevent interleaved output, notes that on all platforms the newline is only `\n`, and advises using `io::stdout().lock()` if you need to call it in a tight loop [Rust Documentation](https://doc.rust-lang.org/std/macro.println.html).

### Output without the newline

The companion macro `print!` behaves the same way but omits the trailing newline [Rust Documentation](https://doc.rust-lang.org/std/macro.print.html). Together with `eprint!`/`eprintln!` they form the core of Rust’s formatted output toolbox [Rust Documentation](https://doc.rust-lang.org/rust-by-example/hello/print.html?utm_source=gabor).

## 2\. Why `println!` is a macro

-   **Variadic arguments without varargs.** Rust purposely has no C‐style variadic functions; a macro can accept any number of expressions, so `println!("x = {}", x)` works with one placeholder or many.
-   **Compile-time format checking.** During macro expansion the compiler parses the format string and type-checks every placeholder, so mismatches are caught before code generation [Rust Documentation](https://doc.rust-lang.org/beta/std/fmt/struct.Arguments.html?utm_source=gabor).
-   **Zero-cost conveniences.** The macro implicitly borrows its arguments (so `println!("{}", s)` does not move `s`) and inlines the call site, eliminating the overhead that a run-time formatting dispatcher would incur .


Macros in Rust run **before** ordinary type checking, which lets them generate code that would be impossible or far more verbose to write by hand [Rust Documentation](https://doc.rust-lang.org/reference/macros.html?utm_source=gabor).

## 3\. Printing without macros

Rust provides the same capability through the `Write` trait:

```rust
use std::io::{self, Write};

fn main() -> io::Result<()> {
    io::stdout().write_all(b"Hello, world!\n")?;
    Ok(())
}
```

`write_all` sends the raw bytes to the stream, and you can combine it with `format!` if you still want the formatting syntax [Rust Documentation](https://doc.rust-lang.org/beta/std/io/fn.stdout.html?utm_source=gabor). Locking the handle (`stdout().lock()`) avoids per-call mutex contention if you need high throughput [Rust Documentation](https://doc.rust-lang.org/beta/std/io/struct.Stdout.html?utm_source=gabor). There is no single `println` function in `std`; the canonical alternatives are the `write!`/`writeln!` macros that take an explicit writer [Rust Documentation](https://doc.rust-lang.org/rust-by-example/hello/print.html?utm_source=gabor).

## 4\. Other frequently used standard macros

| Category                          | Macro                                                                                                                                                                                    | Typical use                       |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| **Formatted output**              | `print!`, `println!`, `eprint!`, `eprintln!`, `format!`, `write!`, `writeln!` [Rust Documentation](https://doc.rust-lang.org/rust-by-example/hello/print.html?utm_source=gabor)    | Console and string formatting     |
| **Debugging**                     | `dbg!` prints an expression together with file/line and returns the value [Rust Documentation](https://doc.rust-lang.org/std/macro.dbg.html?utm_source=gabor)                      | Quick “printf” style debugging    |
| **Assertions & panics**           | `assert!`, `assert_eq!`, `assert_ne!`, `debug_assert!`, `panic!`, `todo!`, `unimplemented!` [Rust Documentation](https://doc.rust-lang.org/std/macro.assert.html?utm_source=gabor) | Invariant checking and stubs      |
| **Collections**                   | `vec!` creates and fills a `Vec` (`vec![1, 2, 3]`, `vec![0; 1024]`) [Rust Documentation](https://doc.rust-lang.org/std/macro.vec.html?utm_source=gabor)                            | Convenient container construction |
| **Logging (via the `log` crate)** | `error!`, `warn!`, `info!`, `debug!`, `trace!` dispatch to the active logger [Docs.rs](https://docs.rs/log?utm_source=gabor)                                                       | Structured, leveled logging       |

These macros are ubiquitous because they give you **concise syntax and compile-time guarantees** without introducing run-time cost.

___

### In short

-   `println!` is a **macro** to support variadic, type-checked formatting at compile time.
-   There is **no function named `println`**; the closest non-macro alternative is manually writing to `stdout`.
-   Rust’s macro set is intentionally small but powerful—learn the handful above and you will cover most day-to-day needs.
-   
---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*