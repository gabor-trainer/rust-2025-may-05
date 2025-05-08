# Generic Error 

Sometimes, a function might need to return an error, but you don't know *exactly* what type of error it will be. It could be an I/O error, a parsing error, a database error, or something else entirely, depending on what happens inside the function or what functions it calls. Creating a custom `enum` error type that lists *every single possible error variant* can become cumbersome, especially if you're calling many different functions from various libraries.

The "simpler approach" described in the our book uses a **trait object** (`dyn std::error::Error`) boxed on the heap (`Box`) to represent "any type that implements the `std::error::Error` trait". The `Send`, `Sync`, and `'static` bounds are added to make this error type suitable for use in multithreaded contexts and ensure the underlying type is valid for the program's lifetime.

By defining a type alias `GenericError = Box<dyn std::error::Error + Send + Sync + 'static>`, you create a single type that can hold *any* error that meets those criteria.

Then, by defining `GenericResult<T> = Result<T, GenericError>`, you have a `Result` type where the error variant can be this generic `GenericError`.

**How the `?` Operator Works with `GenericError`:**

This is where the magic happens, thanks to a standard library implementation of the `From` trait:

The Rust standard library provides an implementation of `From<E> for Box<dyn std::error::Error + Send + Sync + 'static>` for *any* type `E` that itself implements `std::error::Error + Send + Sync + 'static`.

```rust
// (Simplified, conceptual implementation within the standard library)
impl<E> From<E> for Box<dyn std::error::Error + Send + Sync + 'static>
where
    E: std::error::Error + Send + Sync + 'static,
{
    fn from(err: E) -> Self {
        Box::new(err) // Box the original concrete error value
    }
}
```

Because of this `From` implementation, when you use the `?` operator on a `Result<T, E>` inside a function that returns `GenericResult<U>` (which is `Result<U, GenericError>`), the `?` operator automatically calls `GenericError::from(err)` if `Result<T, E>` is `Err(err)`. This converts the specific error `err` (of type `E`) into a `GenericError` (which is `Box<dyn Error>`) and returns it from the function.

**The Manual Conversion (`GenericError::from()`):**

The quote also points out that the `?` operator's behavior is just a shorthand for calling `From::from()` (or specifically `GenericError::from()` in this context). You can manually convert any compatible error type `E` to a `GenericError` by calling `GenericError::from(my_error_of_type_E)`. This is exactly what the `?` operator does internally when propagating an `Err`.

**The Downside:**

As the quote mentions, the main drawback is that the function's return type (`GenericResult`) no longer tells the caller *what specific kinds of errors* they might receive. They just know it's "some error". The caller would need to use dynamic downcasting (checking the concrete type inside the `Box<dyn Error>`) if they needed to handle specific error types differently, which is less convenient than pattern matching on a custom enum.

**Example:**

Let's create a function that reads a file and tries to parse a number from the first line. This function could fail in two ways:
1.  File I/O error (e.g., file not found). This is `std::io::Error`.
2.  Parsing error (e.g., the line isn't a valid number). This is `std::num::ParseIntError`.

We'll use `GenericResult` so we don't have to define a custom error enum encompassing both `io::Error` and `ParseIntError`.

```rust
use std::fs;
use std::io;
use std::error::Error; // Need to import the Error trait
use std::str::FromStr; // Needed for parse()

// Define the generic error type alias
type GenericError = Box<dyn Error + Send + Sync + 'static>;

// Define the generic result type alias
type GenericResult<T> = Result<T, GenericError>;

// Function that might return different error types
fn read_and_parse_first_number(file_path: &str) -> GenericResult<i32> {
    // Open the file. Returns Result<File, io::Error>.
    // If Err(io::Error), ? automatically converts io::Error to GenericError
    // and returns Err(GenericError) from this function.
    let contents = fs::read_to_string(file_path)?;

    // Get the first line.
    let first_line = contents.lines().next()
        .ok_or_else(|| io::Error::new(io::ErrorKind::InvalidData, "File is empty"))?;
        // ok_or_else turns None into an Err. We manually create an io::Error here.
        // The ? operator will then convert this io::Error into a GenericError.

    // Parse the first line as an i32. Returns Result<i32, ParseIntError>.
    // If Err(ParseIntError), ? automatically converts ParseIntError to GenericError
    // and returns Err(GenericError) from this function.
    let number = i32::from_str(first_line)?;

    // If we got here, everything succeeded.
    Ok(number)
}

fn main() {
    // Case 1: File exists and contains a valid number
    match read_and_parse_first_number("test_file.txt") {
        Ok(num) => println!("Successfully read and parsed: {}", num),
        Err(e) => println!("Error processing file: {}", e), // We only know it's a GenericError here
    }

    println!("---");

    // Case 2: File does not exist (io::Error)
    match read_and_parse_first_number("non_existent_file.txt") {
        Ok(num) => println!("Successfully read and parsed: {}", num),
        Err(e) => println!("Error processing file: {}", e), // Error will be GenericError wrapping io::Error
    }

    println!("---");

    // Case 3: File exists but has invalid content (ParseIntError)
    // Need to create 'invalid_file.txt' with non-numeric content first
    // (e.g., create a file named invalid_file.txt and put "hello" in it)
    fs::write("invalid_file.txt", "hello").expect("Failed to create invalid_file.txt");
    match read_and_parse_first_number("invalid_file.txt") {
        Ok(num) => println!("Successfully read and parsed: {}", num),
        Err(e) => println!("Error processing file: {}", e), // Error will be GenericError wrapping ParseIntError
    }
    // Clean up the temporary file (optional)
    fs::remove_file("invalid_file.txt").unwrap();
}

// You would need to create a file named 'test_file.txt' with something like "123" in it
// for the first case to succeed.
// echo "123" > test_file.txt
```

**Explanation of the Example:**

1.  We define `GenericError` and `GenericResult` as described.
2.  `read_and_parse_first_number` is defined to return `GenericResult<i32>`.
3.  `fs::read_to_string("...")?`: This returns `Result<String, io::Error>`. If it's an `Err(io_err)`, the `?` operator sees that `io_err` (type `io::Error`) needs to be converted to the function's return error type (`GenericError`). Since `GenericError::from(io::Error)` exists, `?` calls it, creates `Box::new(io_err)`, wraps it in `Err`, and returns from the function.
4.  `.ok_or_else(...)?`: This part handles the case where the file is empty, converting the `None` from `lines().next()` into an `Err` with a manually created `io::Error`. Again, the `?` operator sees this `io::Error` and converts it to `GenericError` using `From`.
5.  `i32::from_str(first_line)?`: This returns `Result<i32, ParseIntError>`. If it's an `Err(parse_err)`, the `?` operator sees that `parse_err` (type `ParseIntError`) needs to be converted to `GenericError`. Since `GenericError::from(ParseIntError)` also exists, `?` calls it, creates `Box::new(parse_err)`, wraps it in `Err`, and returns.
6.  In `main`, when handling the `Err(e)` case, the type of `e` is `GenericError`. We can print it using `{}` because `GenericError` (specifically the `Box<dyn Error>`) implements `Display` by forwarding the call to the underlying error's `Display` implementation (if it has one, which standard errors do). However, we cannot easily pattern match on `e` to see if it was originally an `io::Error` or a `ParseIntError` without extra steps (dynamic downcasting).

This example demonstrates how `GenericResult` and the `?` operator allow functions to return a single, uniform error type regardless of the specific source of the error, simplifying function signatures but potentially making specific error handling harder for the caller.

## **trait object** (`dyn std::error::Error`)... what?...

1.  **`std::error::Error`**: This is the core trait for representing errors in Rust. Any type that implements this trait is considered a valid error type according to standard conventions. The `Error` trait requires `Debug` and `Display` (so you can print the error) and has methods to get the "source" error (if one error was caused by another).

2.  **`dyn std::error::Error`**: This is a **trait object**. Instead of holding a specific, concrete type (like `std::io::Error` or `std::num::ParseIntError`), it holds a pointer (or pointers, internally) to a value of *some* type that implements the `std::error::Error` trait. You don't know the exact underlying type at compile time; you only know it acts like an `Error`. This enables **dynamic dispatch** – calling methods (`fmt`, `source`, etc.) on the `Error` trait through the trait object pointer at runtime.

3.  **`Box<dyn std::error::Error>`**: Trait objects `dyn Trait` do not have a fixed size that is known at compile time (because the concrete type they hold could be anything implementing the trait, and those types can have different sizes). To store or pass around a trait object, you need to put it in a pointer type whose size *is* known at compile time. `Box<T>` is Rust's standard way to allocate `T` on the heap and get a fixed-size pointer to it. So, `Box<dyn std::error::Error>` means "a pointer on the stack to a value on the heap, where the value on the heap is some type that implements `std::error::Error`".

4.  **`+ Send + Sync`**: These are **trait bounds** added to the trait object.
    *   **`Send`**: This bound means that the underlying concrete error type must be safe to transfer ownership of *between threads*. If you have a `Box<dyn Error + Send>`, you can move that box from one thread to another.
    *   **`Sync`**: This bound means that the underlying concrete error type must be safe to share references to *across threads*. If you have a `&Box<dyn Error + Sync>`, you can send that reference to another thread (specifically, `&T` is `Send` if `T` is `Sync`).
    Adding `Send + Sync` makes this generic error type safe to use in multithreaded applications, which is common when errors need to propagate out of worker threads.

5.  **`+ 'static`**: This is a **lifetime bound**. It means that the underlying concrete error type must have a `'static` lifetime. A `'static` lifetime means that any data referenced by the error (if it contains references) must live for the entire duration of the program. This is typically true for error types that own their data (like `String`s) or refer to static data, but it rules out errors that might hold references to temporary data on the stack. This bound ensures the error is valid for as long as the `Box` holding it exists, even if that's for the entire program lifetime.


(Libraries like `anyhow` and `thiserror` build upon these concepts to provide even more ergonomic ways to work with generic and specific error types, respectively. `anyhow::Error`, for instance, is essentially a more feature-rich wrapper around a `Box<dyn Error + Send + Sync>`.)

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*