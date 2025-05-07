# Checked, Wrapping, Saturating, and Overflowing Arithmetic

**1. Checked Arithmetic (`checked_add`, `checked_sub`, `checked_mul`, etc.)**

*   **Behavior:** Performs the arithmetic operation and returns `Some(result)` if the operation succeeds without overflow or underflow. If overflow or underflow *would* occur, it returns `None`, signaling the failure explicitly.
*   **Use Case:** When you want to detect potential overflow/underflow and handle it gracefully, rather than panicking or silently wrapping.
*   **Return Type:** `Option<Self>` (where `Self` is the integer type).

*   **Code Examples:**

    ```rust
    // i8 (range: -128 to 127)
    let x: i8 = 120;
    let y: i8 = 10;
    let z: i8 = -128;

    println!("Checked i8 add (success): {:?}", x.checked_add(5)); // Some(125)
    println!("Checked i8 add (overflow): {:?}", x.checked_add(y)); // None
    println!("Checked i8 sub (success): {:?}", x.checked_sub(10)); // Some(110)
    println!("Checked i8 sub (underflow): {:?}", z.checked_sub(1)); // None

    // u8 (range: 0 to 255)
    let a: u8 = 250;
    let b: u8 = 10;
    let c: u8 = 5;

    println!("Checked u8 add (success): {:?}", a.checked_add(c)); // Some(255)
    println!("Checked u8 add (overflow): {:?}", a.checked_add(b)); // None
    println!("Checked u8 sub (success): {:?}", a.checked_sub(c)); // Some(245)
    println!("Checked u8 sub (underflow): {:?}", c.checked_sub(b)); // None
    ```

---

**2. Wrapping Arithmetic (`wrapping_add`, `wrapping_sub`, `wrapping_mul`, etc.)**

*   **Behavior:** Ignores overflow/underflow and performs the operation using two's complement (for signed integers) or standard modular arithmetic (for unsigned integers). The result wraps around. `MAX + 1` becomes `MIN`, `MIN - 1` becomes `MAX` (for signed); `MAX + 1` becomes `0`, `0 - 1` becomes `MAX` (for unsigned).
*   **Use Case:** When you specifically need modular arithmetic (e.g., hash functions, cyclic counters) and don't care about or want to detect overflow. This is often the behavior of the default operators in *release* builds.
*   **Return Type:** `Self`.

*   **Code Examples:**

    ```rust
    // i8 (range: -128 to 127)
    let x: i8 = 127;
    let y: i8 = -128;

    println!("Wrapping i8 add (overflow): {}", x.wrapping_add(1)); // -128
    println!("Wrapping i8 sub (underflow): {}", y.wrapping_sub(1)); // 127

    // u8 (range: 0 to 255)
    let a: u8 = 255;
    let b: u8 = 0;

    println!("Wrapping u8 add (overflow): {}", a.wrapping_add(1)); // 0
    println!("Wrapping u8 sub (underflow): {}", b.wrapping_sub(1)); // 255
    ```

---

**3. Saturating Arithmetic (`saturating_add`, `saturating_sub`, `saturating_mul`, etc.)**

*   **Behavior:** If the operation would result in a value greater than the type's maximum, it returns the maximum value. If it would result in a value less than the type's minimum, it returns the minimum value. The result is clamped to the type's range.
*   **Use Case:** When you want values to stay within the valid range, clipping at the boundaries rather than wrapping or failing. Common in graphics, signal processing, or financial calculations where values shouldn't exceed certain limits.
*   **Return Type:** `Self`.

*   **Code Examples:**

    ```rust
    // i8 (range: -128 to 127)
    let x: i8 = 100;
    let y: i8 = 50; // Will cause overflow when added to 100

    println!("Saturating i8 add (success): {}", x.saturating_add(20)); // 120
    println!("Saturating i8 add (overflow): {}", x.saturating_add(y)); // 127 (max value)
    println!("Saturating i8 sub (underflow): {}", (-120i8).saturating_sub(10)); // -128 (min value)

    // u8 (range: 0 to 255)
    let a: u8 = 200;
    let b: u8 = 100; // Will cause overflow when added to 200

    println!("Saturating u8 add (success): {}", a.saturating_add(50)); // 250
    println!("Saturating u8 add (overflow): {}", a.saturating_add(b)); // 255 (max value)
    println!("Saturating u8 sub (underflow): {}", 5u8.saturating_sub(10)); // 0 (min value)
    ```

---

**4. Overflowing Arithmetic (`overflowing_add`, `overflowing_sub`, `overflowing_mul`, etc.)**

*   **Behavior:** Performs the operation, and the result wraps around (like wrapping arithmetic). Additionally, it returns a boolean flag indicating whether overflow or underflow occurred during the operation.
*   **Use Case:** When you need both the wrapped result *and* to know if the operation went out of bounds. Useful for implementing arbitrary-precision arithmetic or detecting overflows without panicking.
*   **Return Type:** `(Self, bool)` - a tuple containing the wrapped result and a boolean (`true` if overflowed, `false` otherwise).

*   **Code Examples:**

    ```rust
    // i8 (range: -128 to 127)
    let x: i8 = 120;
    let y: i8 = 10;

    println!("Overflowing i8 add (success): {:?}", x.overflowing_add(5)); // (125, false)
    println!("Overflowing i8 add (overflow): {:?}", x.overflowing_add(y)); // (-128, true)

    // u8 (range: 0 to 255)
    let a: u8 = 250;
    let b: u8 = 10;

    println!("Overflowing u8 add (success): {:?}", a.overflowing_add(5)); // (255, false)
    println!("Overflowing u8 add (overflow): {:?}", a.overflowing_add(b)); // (0, true)
    println!("Overflowing u8 sub (underflow): {:?}", 5u8.overflowing_sub(10)); // (251, true)
    ```

---

**Floating-Point (`f32`, `f64`) Note:**

Floating-point arithmetic behaves differently on overflow/underflow. Operations that would exceed the representable range result in positive or negative **Infinity** (`inf` or `-inf`). Underflow typically results in zero or a subnormal number. Division by zero results in `inf` (for non-zero numerator) or **NaN** (Not-a-Number, for 0/0 or inf/inf). The `checked_`, `wrapping_`, `saturating_`, and `overflowing_` methods discussed above **do not exist** for floating-point types.

---

**Cheatsheet Table:**

| Method Type | Behavior on Overflow/Underflow | Return Type    | Applicable Types |
| :---------- | :----------------------------- | :------------- | :--------------- |
| Checked     | Returns `None`                 | `Option<Self>` | All Integers     |
| Wrapping    | Wraps around min/max           | `Self`         | All Integers     |
| Saturating  | Clamps to min/max value        | `Self`         | All Integers     |
| Overflowing | Wraps & returns boolean flag   | `(Self, bool)` | All Integers     |


## The Default Behavior of Standard Operators

Rust's default behavior for integer overflow/underflow with standard operators (`+`, `-`, `*`, etc.) is designed for safety during development and performance in production:

*   **Debug Builds (compiled with `cargo build` or `cargo run`):** Integer overflow/underflow is considered a **logic error**. The program will **panic!** at runtime, clearly indicating where the overflow occurred. This helps catch bugs early during development.
*   **Release Builds (compiled with `cargo build --release` or `cargo run --release`):** Integer overflow/underflow is **ignored**. The operation performs a **wrapping** calculation, as if you had used the `wrapping_*` methods. The result will be the wrapped value, and no panic occurs. This provides maximum performance, assuming the developer has already handled or reasoned about potential overflows.

**Use Case & Example (Multiplication Overflow with `u8`)**

Imagine you are processing sensor readings, which are small integer values, and you want to calculate their product. Suppose these readings are stored as `u8` (unsigned 8-bit integers, range 0-255).

```rust
// Example demonstrating default '*' operator behavior

fn main() {
    let reading1: u8 = 200;
    let reading2: u8 = 2;

    // Calculate the product
    // Mathematically, 200 * 2 = 400
    // But u8 can only hold up to 255
    let product = reading1 * reading2;

    println!("Reading 1: {}", reading1);
    println!("Reading 2: {}", reading2);
    println!("Calculated product: {}", product);
}

// To run in Debug:
// cargo run

// To run in Release:
// cargo run --release
```

**Behavior in Debug Build (`cargo run`)**

```text
Reading 1: 200
Reading 2: 2
thread 'main' panicked at 'attempt to multiply with overflow', src/main.rs:9:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

*   **Explanation:** In a debug build, the compiler includes runtime checks for arithmetic overflow. When the multiplication `200 * 2` attempts to produce the value 400, which is larger than the maximum value for `u8` (255), the check triggers a `panic!`. The program stops immediately, and a clear error message is printed, pointing to the line where the overflow occurred. This is helpful for identifying potential bugs during development.

**Behavior in Release Build (`cargo run --release`)**

```text
Reading 1: 200
Reading 2: 2
Calculated product: 144
```

*   **Explanation:** In a release build (which is optimized for performance), the compiler typically *removes* the runtime overflow checks. The multiplication is performed using the underlying hardware's integer arithmetic, which usually wraps around on overflow for unsigned types.
    *   The result of `200 * 2` in 8 bits is 400.
    *   In unsigned 8-bit wrapping arithmetic, 400 wraps around the range 0-255.
    *   `400 % 256 = 144`.
    *   So, the variable `product` gets assigned the value `144` without any error or warning being reported at runtime. The program continues execution with this potentially incorrect value.

**Conclusion:**

The default behavior provides a development-time safety net (panic in debug) and production-time performance (wrapping in release). However, for scenarios where you need consistent, explicit handling of potential overflow (like financial calculations, cryptography, or robust input processing), you should always use the `checked_*`, `wrapping_*`, `saturating_*`, or `overflowing_*` methods to make your intent clear and ensure predictable behavior regardless of the build profile.


## Why `Option<Self>` instead of `Result<Self, ErrorType>`?

The `checked_*` methods return `Option<Self>` instead of `Result<Self, ErrorType>` as we've seen when we were ctrl+click digging deep in the code editor.  This is an intentional decision because the **failure mode for checked arithmetic is a simple, undifferentiated outcome: the mathematical result is outside the representable range of the type.**

** Recall what we know so far about `Option` and `Result`: **

1.  **`Option<T>`:** Represents a value that **may or may not exist**. `Some(T)` means the value is present; `None` means it is absent. `Option` is typically used for cases where failure is just the *lack* of a successful outcome, without a complex reason for the failure.
2.  **`Result<T, E>`:** Represents an operation that can have one of two distinct outcomes: **success (`Ok(T)`) with a result, or failure (`Err(E)`) with specific error information.** `Result` is used when the failure *itself* carries meaningful context about *why* the operation failed (e.g., `std::io::Error` for file operations, `std::num::ParseIntError` for parsing).

For integer overflow or underflow in `checked_*` methods:

*   If `a.checked_add(b)` succeeds, the result `a + b` fit within the type, and you get `Some(a + b)`.
*   If `a.checked_add(b)` fails (overflows), the result `a + b` did *not* fit within the type. There isn't a complex "error" to describe *why* it didn't fit, just that the mathematical result was too large or too small. The failure isn't categorized into different types (like "file not found" vs "permission denied").

Using `Option` is simpler and more idiomatic in this case because `None` perfectly captures the state "the intended numerical result is not representable by this type." A `Result` would require defining an `ErrorType` (perhaps an empty struct or enum) just to signal "overflow/underflow occurred," which adds unnecessary boilerplate compared to the simple `None`.

**To be short:** `Option` signifies "no value could be produced," while `Result` signifies "an operation failed for a specific reason." Checked integer arithmetic falls into the "no value could be produced (within the type's limits)" category.    

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*