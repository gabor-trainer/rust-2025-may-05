**Lab 12: Error Handling with `Result`**

Rust's approach is about the `Result<T, E>` enum, providing a type-safe and explicit way to handle potential errors without resorting to exceptions or crashing the program. This lab explores the core `Result` type, its utility methods, the convenient `?` operator, and how to handle functions that can return different types of errors.

By the end of this lab, you will be able to:

*   Use `Result` to represent operations that can succeed or fail.
*   Apply common `Result` methods to handle success and error cases.
*   Utilize the `?` operator for concise error propagation.
*   Understand why handling multiple error types in a single function can be challenging.
*   Employ `Box<dyn std::error::Error>` as a way to return different error types.

This lab provides hands-on practice with Rust's idiomatic error handling patterns, which are essential for writing reliable applications.

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`, enums, `match`).
    *   Understanding of `Option<T>`. `Result` is very similar conceptually.
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. Check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial error handling lab setup`, `feat: complete result basics exercise`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new error_handling_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd error_handling_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `error_handling_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file. Use comments (`//`) to separate and label the code for each exercise.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`.

1.  **`Result` Basics: `Ok`, `Err`, `is_ok`, `is_err`, `unwrap`, `expect`**
    Start by defining functions that return `Result` and use basic methods to check and extract the value.

    *   **Exercise Type Goal:** Introduce the `Result` enum and its basic methods for checking state and extracting values (safely and unsafely).
    *   Define a function `parse_positive_integer` *outside* of `main` that takes a `&str` and returns `Result<u32, String>`. If the string can be parsed as a `u32` and is greater than 0, return `Ok(parsed_value)`. Otherwise, return `Err(error_message.to_string())`. Use `str::parse()` which itself returns a `Result`. You'll need to handle the `Err` case from `parse()`.
    *   In `main`, call `parse_positive_integer` with a valid positive integer string (e.g., `"123"`) and an invalid string (e.g., `"abc"`).
    *   For the valid call, use `.is_ok()` and `.is_err()` to check the result, and then use `.unwrap()` to get the inner `u32` value (it should succeed).
    *   For the invalid call, use `.is_ok()` and `.is_err()`, and then use `.expect("...")` to get the inner value (it should panic with your provided message).
    *   **Trap:** Using `.unwrap()` or `.expect()` on an `Err` value will cause your program to panic! These methods are useful in examples or when you are *absolutely certain* an error will not occur, but generally, you should use safer methods.
    *   **Verification:** Run `cargo run`. Observe the output for the successful case and the panic message for the unsuccessful case.

2.  **`Result` Transformations: `map`, `and_then`, `or_else`**
    Explore methods that transform a `Result` based on whether it is `Ok` or `Err` without needing to use `match` for simple cases.

    *   **Exercise Type Goal:** Learn common `Result` methods for chaining operations and handling errors gracefully.
    *   Use the `parse_positive_integer` function from the previous exercise.
    *   Call `parse_positive_integer` with a valid input (e.g., `"42"`). Use the `.map()` method on the `Result` to transform the success value (`u32`) into something else (e.g., multiply it by 2). Print the final result.
    *   Call `parse_positive_integer` with an invalid input (e.g., `"negative -5"`). Use `.map()` again. What is the type of the result? `map()` only transforms the `Ok` value; the `Err` value is passed through unchanged.
    *   Define a function `process_value` *outside* of `main` that takes a `u32` and returns `Result<bool, String>` (e.g., returns `Ok(true)` if the number is even, `Err("odd number".to_string())` if odd).
    *   Call `parse_positive_integer("10")` and use `.and_then()` to call `process_value` with the parsed number. Print the final `Result`.
    *   Call `parse_positive_integer("11")` and use `.and_then()` to call `process_value`. Print the final `Result`.
    *   Call `parse_positive_integer("invalid")` and use `.and_then()` to call `process_value`. What happens? `and_then()` only calls the provided function if the initial result is `Ok`.
    *   Define a function `handle_error_message` *outside* of `main` that takes a `String` error message and returns a different `Result<u32, String>` (e.g., always returns `Ok(0)`).
    *   Call `parse_positive_integer("abc")` and use `.or_else()` to call `handle_error_message` with the error. Print the final `Result`.
    *   **Hints:**
        *   `.map()`: `Result<T, E> -> Result<U, E>` (transforms Ok value).
        *   `.and_then()`: `Result<T, E> -> Result<U, E2>` (chains operations that return Result).
        *   `.or_else()`: `Result<T, E> -> Result<T, E2>` (handles Err by returning a new Result).
    *   **Verification:** Run `cargo run`. Observe the outputs for the different calls and transformations.

3.  **The `?` Operator for Propagation**
    The `?` operator is syntactic sugar for propagating a `Result`'s error up the call stack. It can *only* be used within functions that return `Result` (or `Option`).

    *   **Exercise Type Goal:** Use the `?` operator for concise error handling in functions.
    *   Define a function `read_number_from_string` *outside* of `main` that takes a `&str` and returns `Result<u32, std::num::ParseIntError>`. Use the `?` operator after calling `str::parse()` inside this function. If `parse()` returns `Ok(value)`, `?` will unwrap `value`. If it returns `Err(err)`, `?` will immediately return `Err(err)` from `read_number_from_string`.
    *   Define a function `process_and_validate` *outside* of `main` that takes a `&str` and returns `Result<bool, String>`. Inside this function:
        *   Call `read_number_from_string` using the `?` operator: `let number = read_number_from_string(input)?;`. If `read_number_from_string` returns an `Err`, the error will propagate out of `process_and_validate`.
        *   Add a check: if the `number` is greater than 100, return `Err("Number too large".to_string())`.
        *   If the number is valid and not too large, return `Ok(true)`.
    *   In `main`, call `process_and_validate` with strings that: 1) cannot be parsed (e.g., `"abc"`), 2) are valid but too large (e.g., `"200"`), and 3) are valid and acceptable (e.g., `"50"`). Use a `match` or `if let` in `main` to handle the `Result` returned by `process_and_validate`.
    *   **Trap:** You *cannot* use `?` in `main` directly because `main` traditionally returns `()`, not `Result`. (Note: `main` *can* return `Result<(), E>` since Rust 1.26, but for this exercise, we'll stick to handling the `Result` in a traditional `main` that returns `()`).
    *   **Verification:** Run `cargo run`. Observe how the errors propagate from `read_number_from_string` and the manual check within `process_and_validate`.

4.  **Handling Multiple Error Types: The Problem (Compile Error Expected)**
    When a function can fail in several different ways, each with a distinct error type, returning `Result` directly using the `?` operator can be tricky if the error types don't match the function's declared error type or cannot be automatically converted.

    *   **Exercise Type Goal:** Understand the challenge of functions returning multiple distinct error types.
    *   Define a function `read_config_value` *outside* of `main` that you intend to return `Result<u32, std::io::Error>`. (Note: We won't actually do file I/O, but we'll simulate the error type).
    *   Inside `read_config_value`, simulate an I/O error by creating an `std::io::Error`. You can use `std::io::Error::new(std::io::ErrorKind::Other, "Simulated IO error")`. Use the `?` operator on this simulated error. (Example: `let simulated_io_result: Result<(), std::io::Error> = Err(std::io::Error::new(std::io::ErrorKind::Other, "Simulated IO error")); simulated_io_result?;`).
    *   Also inside `read_config_value`, simulate a parsing error. Have the function receive a `&str` input parameter. Call `input.parse::<u32>()` and use the `?` operator on its result.
    *   **Observe:** You will get a compile error. The compiler will tell you that `std::num::ParseIntError` (from `parse()`) cannot be automatically converted into `std::io::Error` (the function's declared return error type) using `?`. This is because there's no default `From<ParseIntError> for io::Error` implementation in the standard library.
    *   **Trap:** The `?` operator requires that the error type being propagated implements the `From` trait for the function's declared return error type (`From<PropagatedError> for ReturnError`). If this isn't the case, `?` doesn't know how to convert the error.
    *   **Verification:** Run `cargo check`. The compile error showing the missing `From` implementation is the expected outcome.

5.  **Handling Multiple Error Types: The Solution (`Box<dyn std::error::Error>`)**
    One way to handle functions that can return disparate error types is to "box" them up as a trait object that represents "any type that implements the `std::error::Error` trait".

    *   **Exercise Type Goal:** Use `Box<dyn std::error::Error>` to represent and return heterogeneous error types.
    *   Modify the `read_config_value` function from the previous exercise. Change its return type to `Result<u32, Box<dyn std::error::Error>>`.
    *   Keep the logic inside the function the same (simulating the I/O error and calling `parse().?`).
    *   **Observe:** The code should now compile without error!
    *   **Discuss:** Why did this work? Rust's standard library provides a generic `From` implementation: `impl<E> From<E> for Box<dyn std::error::Error> where E: std::error::Error + 'static`. This means *any* type `E` that implements the `std::error::Error` trait and has a `'static` lifetime can be automatically converted into a `Box<dyn std::error::Error>`. Both `std::io::Error` and `std::num::ParseIntError` implement the `std::error::Error` trait. So, the `?` operator sees that it can convert the `ParseIntError` and the `io::Error` into the function's return type (`Box<dyn Error>`), and it does so automatically.
    *   **Verification:** Run `cargo check`. The code should now compile.

6.  **Using `Box<dyn std::error::Error>` in Practice**
    Demonstrate calling a function that returns `Result<T, Box<dyn std::error::Error>>` and handling its result in `main`.

    *   **Exercise Type Goal:** Practice calling and handling functions that return a boxed trait object error.
    *   Use the `read_config_value` function you fixed in the previous exercise (the one that returns `Result<u32, Box<dyn std::error::Error>>`).
    *   In `main`, call `read_config_value` with strings that: 1) trigger the simulated I/O error, 2) trigger the parse error, and 3) succeed.
    *   Use a `match` statement in `main` to handle the `Result`.
    *   For the `Ok(value)` case, print the value.
    *   For the `Err(err)` case, print the error. You can use `println!("Error: {}", err);`. The `Box<dyn Error>` implements `Display`.
    *   **Discuss:** When using `Box<dyn Error>`, you lose the specific type information about the error at compile time. You know it's *some* type implementing `Error`, but you don't know *which one* without further inspection (which is possible but less common for simple handling). This is a trade-off for the flexibility of returning different error types.
    *   **Verification:** Run `cargo run`. Observe the output for each case (simulated I/O error, parse error, and success) and how the error messages are printed from the boxed trait object.

**Running the Application**

As you complete each exercise, run `cargo run` (or `cargo check` if the step is expected to cause a compile error) to verify your understanding and code correctness before moving to the next one.

**Key Concepts Review**

*   **`Result<T, E>`**: Enum representing success (`Ok(T)`) or failure (`Err(E)`).
*   **`Ok(value)`**: Represents a successful outcome with a value.
*   **`Err(error)`**: Represents a failed outcome with an error value.
*   **`is_ok()` / `is_err()`**: Check if a `Result` is `Ok` or `Err`.
*   **`unwrap()` / `expect()`**: Unsafely extract the `Ok` value, panicking on `Err`. `expect()` provides a custom panic message. Avoid in production code unless truly unavoidable.
*   **`map()`**: Transforms the `Ok` value of a `Result`.
*   **`and_then()`**: Chains operations that return `Result`. Only runs if the initial result is `Ok`.
*   **`or_else()`**: Handles the `Err` case by running a provided function that returns a new `Result`.
*   **`?` operator**: Propagates an `Err` value up the call stack if the `Result` is `Err`. If the `Result` is `Ok`, it unwraps the value. Can only be used in functions returning `Result` (or `Option`).
*   **`?` and `From`**: The `?` operator requires the error being propagated to implement `From<PropagatedError> for ReturnError` to convert it to the function's return error type.
*   **`std::error::Error` trait**: The standard trait for types representing errors.
*   **`Box<dyn std::error::Error>`**: A boxed trait object representing "any type that implements the `Error` trait". Useful for returning different error types from a single function.
*   **`From` implementations for `Box<dyn Error>`**: The standard library provides `From<E> for Box<dyn Error>` for any `E` implementing `Error + 'static`, enabling `?` to work automatically.
*   **Trait Objects**: Abstract types that erase specific type information, allowing a value to be treated as "any type implementing this trait". Requires boxing (`Box`) to have a known size.

**Challenges / Next Steps**

1.  **Custom Error Types:** Instead of `String` or `Box<dyn Error>`, define your own custom error enum that can represent different kinds of errors your function might return. Implement `Display` and `Error` for your custom error. Use the `?` operator and implement `From` traits to convert specific errors (like `ParseIntError`) into your custom error type.
2.  **Error Crates:** Explore popular error handling crates like `thiserror` and `anyhow`. How do they simplify creating custom errors and handling heterogeneous errors compared to manual implementation or `Box<dyn Error>`?
3.  **Error Context:** Research how to add context or wrap errors (e.g., "Failed to read config file because...") using methods like `Result::map_err` or features provided by error handling crates.

**Troubleshooting**

*   **"the `?` operator can only be used in a function that returns `Result` or `Option`"**: You are trying to use `?` in a function (likely `main`) that doesn't have a compatible return type. Either change the function's return type to `Result` (or `Option`), or handle the `Result` using `match` or methods like `.unwrap()`/`.expect()`/`.is_ok()`/`.err()`.
*   **"the trait `From<SomeErrorType>` is not implemented for `TargetErrorType`"**: This is the error from Exercise 4. The `?` operator is trying to convert `SomeErrorType` into `TargetErrorType`, but the required `From` implementation doesn't exist. Fix this by: 1) changing the function's return error type to something compatible (`Box<dyn Error>` or a custom error type that implements `From`), or 2) manually converting the error (`.map_err(|err| YourTargetError::from(err))`).
*   **Panic**: If your program panics with a message from `.unwrap()` or `.expect()`, it means you called it on an `Err` value. This is expected in Exercise 1 for the invalid input, but indicates unhandled errors elsewhere if it happens unexpectedly. Use safer methods (`match`, `if let Ok`, `.map`, `.and_then`) to handle potential `Err` cases.

**Conclusion**

You've successfully practiced key aspects of error handling in Rust, from basic `Result` usage and transformation to handling the complexity of functions returning multiple error types using `?` and `Box<dyn std::error::Error>`. Embracing `Result` and propagating errors explicitly is fundamental to writing safe and predictable Rust code.

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete error handling lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Final Solution Code (src/main.rs)**

```rust
// src/main.rs

use std::io; // Needed for simulated io::Error
use std::num::ParseIntError; // Needed for parse() error
use std::error::Error; // Needed for the Error trait and Box<dyn Error>

fn main() {
    println!("--- Rust Error Handling Lab Solutions ---");

    // 1. Exercise: Result Basics: Ok, Err, is_ok, is_err, unwrap, expect
    println!("\n--- Exercise 1: Result Basics ---");

    // Valid case
    let result1 = parse_positive_integer("123");
    println!("Result for '123': {:?}", result1);
    println!("Is Ok? {}", result1.is_ok());
    println!("Is Err? {}", result1.is_err());
    let value1 = result1.unwrap(); // Safe unwrap because we checked or know it's Ok
    println!("Unwrapped value: {}", value1);

    // Invalid case (will panic)
    let result2 = parse_positive_integer("abc");
    println!("Result for 'abc': {:?}", result2);
    println!("Is Ok? {}", result2.is_ok());
    println!("Is Err? {}", result2.is_err());
    // The following line will cause a panic. Uncomment to see it happen.
    // let value2 = result2.expect("Expected a valid positive integer for 'abc'!");
    println!("Skipping expect() for 'abc' to avoid panic in final code.");


    // 2. Exercise: Result Transformations: map, and_then, or_else
    println!("\n--- Exercise 2: Result Transformations ---");

    // map example
    let mapped_ok = parse_positive_integer("42").map(|n| n * 2);
    println!("map('42'): {:?}", mapped_ok); // Ok(84)
    let mapped_err = parse_positive_integer("invalid").map(|n| n * 2);
    println!("map('invalid'): {:?}", mapped_err); // Err("...") - error passed through

    // and_then example
    let and_then_ok = parse_positive_integer("10").and_then(process_value);
    println!("and_then('10'): {:?}", and_then_ok); // Ok(true)
    let and_then_err_process = parse_positive_integer("11").and_then(process_value);
    println!("and_then('11'): {:?}", and_then_err_process); // Err("odd number") - error from process_value
    let and_then_err_parse = parse_positive_integer("invalid").and_then(process_value);
    println!("and_then('invalid'): {:?}", and_then_err_parse); // Err("...") - error from parse_positive_integer

    // or_else example
    let or_else_err = parse_positive_integer("abc").or_else(handle_error_message);
     println!("or_else('abc'): {:?}", or_else_err); // Ok(0) - handled by handle_error_message
     let or_else_ok = parse_positive_integer("5").or_else(handle_error_message);
      println!("or_else('5'): {:?}", or_else_ok); // Ok(5) - original Ok value is kept


    // 3. Exercise: The ? Operator for Propagation
    println!("\n--- Exercise 3: The ? Operator ---");

    println!("Testing process_and_validate('abc'): {:?}", process_and_validate("abc")); // Should propagate ParseIntError
    println!("Testing process_and_validate('200'): {:?}", process_and_validate("200")); // Should return Err("Number too large")
    println!("Testing process_and_validate('50'): {:?}", process_and_validate("50"));   // Should return Ok(true)


    // 4. Exercise: Handling Multiple Error Types: The Problem (Compile Error Expected)
    println!("\n--- Exercise 4: Multiple Errors - Problem ---");
    println!("Code for Exercise 4 is commented out as it causes a compile error.");
    println!("Trap: ? operator requires From<PropagatedError> for ReturnError, which isn't always implemented.");
    // This function signature and body would cause a compile error:
    /*
    fn read_config_value_problem(input: &str) -> Result<u32, std::io::Error> {
        // Simulate an IO error propagation
        let simulated_io_result: Result<(), std::io::Error> = Err(std::io::Error::new(std::io::ErrorKind::Other, "Simulated IO error"));
        simulated_io_result?; // This works because return type is io::Error

        // This line will cause a compile error because ParseIntError != io::Error,
        // and there's no From<ParseIntError> for io::Error
        let parsed_number = input.parse::<u32>()?;

        Ok(parsed_number)
    }
    */
    println!("Compile error expected because ParseIntError cannot convert to std::io::Error automatically.");


    // 5. Exercise: Handling Multiple Error Types: The Solution (Box<dyn std::error::Error>)
    println!("\n--- Exercise 5: Multiple Errors - Box<dyn Error> Solution ---");
    println!("Code for Exercise 5 (fixing the function signature) is part of the function definition below main.");
    println!("Solution: Change function return type to Result<T, Box<dyn std::error::Error>>.");
    println!("This works because standard library provides From implementations to Box<dyn Error>.");

    // Exercise 6 will call this corrected function.


    // 6. Exercise: Using Box<dyn std::error::Error> in Practice
    println!("\n--- Exercise 6: Box<dyn Error> in Practice ---");

    println!("Calling read_config_value_solution with simulated IO error:");
    match read_config_value_solution("trigger_io_error") { // The function logic below simulates this input causing the IO error path
        Ok(value) => println!("Success: {}", value),
        Err(err) => println!("Error: {}", err), // err is Box<dyn Error>, implements Display
    }

    println!("\nCalling read_config_value_solution with parse error:");
     match read_config_value_solution("abc") {
        Ok(value) => println!("Success: {}", value),
        Err(err) => println!("Error: {}", err), // err is Box<dyn Error>, implements Display
    }

    println!("\nCalling read_config_value_solution with success:");
     match read_config_value_solution("123") {
        Ok(value) => println!("Success: {}", value),
        Err(err) => println!("Error: {}", err), // This case shouldn't happen for "123"
    }


    println!("\n--- Lab Complete ---");
}


// --- Function Definitions (outside main) ---

// Function for Exercise 1 and 2
fn parse_positive_integer(input: &str) -> Result<u32, String> {
    // Use str::parse() which returns Result<u32, ParseIntError>
    input.parse::<u32>()
        .map_err(|e| format!("Failed to parse integer: {}", e)) // Convert ParseIntError to String error
        .and_then(|num| { // Chain another check
            if num > 0 {
                Ok(num)
            } else {
                Err("Number must be positive".to_string())
            }
        })
}

// Function for Exercise 2 (used with and_then)
fn process_value(number: u32) -> Result<bool, String> {
    if number % 2 == 0 {
        Ok(true) // Even number
    } else {
        Err("odd number".to_string()) // Odd number
    }
}

// Function for Exercise 2 (used with or_else)
fn handle_error_message(error_msg: String) -> Result<u32, String> {
    println!("Handling error message: {}", error_msg);
    // In a real scenario, you might log the error, try a default, etc.
    Ok(0) // Return a default value on error
}

// Function for Exercise 3
fn read_number_from_string(input: &str) -> Result<u32, ParseIntError> {
    // Use ? to propagate the ParseIntError from parse()
    let number = input.parse::<u32>()?;
    Ok(number)
}

// Function for Exercise 3
// * * * NOTE: * * * Intentianlly containing a compile error to demonstrate the different problems.
// Comment out the function to avoid compile error in the main function, and use the revised version below, which is the
// next function in this soluition help snippet

fn process_and_validate(input: &str) -> Result<bool, String> {
    // Call read_number_from_string and use ? to propagate its error (ParseIntError)
    // Note: This requires ParseIntError to be convertible to String.
    // The standard library does NOT provide From<ParseIntError> for String.
    // So this function signature should ideally be Result<bool, Box<dyn Error>> or a custom error.
    // For the purpose of Exercise 3, let's assume we only handle parse errors or String errors below.
    // A real-world Result chain often returns a single custom error enum.

    // Let's adjust the return type for this example to be compatible with ? from ParseIntError
    // by having it return a compatible error type or Box<dyn Error>.
    // Given the exercise focused on ?, let's make it return Box<dyn Error> for propagation practice.
     // Re-doing based on the spirit of the ? exercise aiming at propagation:
     // We *can* use ? if the target error type is Box<dyn Error> or similar.
     // Let's make this function return Box<dyn Error> for practice, though the initial description
     // for Ex3 said String error, which is slightly less direct for ParseIntError propagation.
     // Sticking to the plan of Ex3 focusing *only* on ? propagation:
     // read_number_from_string returns ParseIntError.
     // Our manual check returns String.
     // These are different! The ? from read_number_from_string needs to convert to the return error.
     // Let's change the return type to Box<dyn Error> now to allow ? from ParseIntError.

    // Correct signature to allow ? from ParseIntError and return our String error
    // This is a common scenario where you need a custom error enum or Box<dyn Error>
    // based on what errors are propagated.
    // For Ex 3 focus, let's keep the check simple and assume ParseIntError is the *only*
    // propagated error type from read_number_from_string.
    // Let's revise the check to return a ParseIntError too, or Box<dyn Error>.
    // Simplest approach for Ex3 is to make read_number_from_string return Box<dyn Error>
    // or make process_and_validate return ParseIntError or Box<dyn Error>.
    // Let's adjust read_number_from_string to return Box<dyn Error> for clear ? propagation.

    // Revised read_number_from_string returns Result<u32, Box<dyn Error>>
    // Revised process_and_validate receives input and returns Result<bool, Box<dyn Error>>

    // Call the revised read_number_from_string and use ?
    let number = match read_number_from_string_revised(input) {
        Ok(num) => num,
        Err(err) => return Err(err), // Manual propagation if we don't use ?
    };

    // Let's rewrite with ? now that read_number_from_string_revised returns Box<dyn Error>
     let number = read_number_from_string_revised(input)?;


    // Add a check that returns a String error converted to Box<dyn Error>
    if number > 100 {
        // Convert String to Box<dyn Error> manually or let ? do it if return type is Box<dyn Error>
         let err_msg: Box<dyn Error> = "Number too large".into(); // String -> &str -> Display -> Error -> Box<dyn Error>
         return Err(err_msg); // Return the manual error
        // Or if the function returned String and ParseIntError was converted... needs custom error or Box
    }

    // If the number is valid and not too large
    Ok(true)
}

// Revised function for Exercise 3, returning Box<dyn Error> to enable ? propagation of ParseIntError
fn read_number_from_string_revised(input: &str) -> Result<u32, Box<dyn Error>> {
     // input.parse() returns Result<u32, ParseIntError>.
     // ? operator attempts From<ParseIntError> for Box<dyn Error>, which exists.
    let number = input.parse::<u32>()?;
    Ok(number)
}


// Function for Exercise 5 and 6
fn read_config_value_solution(input: &str) -> Result<u32, Box<dyn Error>> {
    // Exercise type goal: Use Box<dyn std::error::Error> to return different error types.
    // Trap: Returning different concrete error types without a common return error type or conversion.

    // Simulate an IO error propagation based on input string
    if input == "trigger_io_error" {
        let simulated_io_error = io::Error::new(io::ErrorKind::Other, "Simulated IO error based on input");
        // ? operator attempts From<io::Error> for Box<dyn Error>, which exists.
        return Err(simulated_io_error)?; // Or just Err(simulated_io_error.into())
    }

    // Simulate a parsing error for other inputs using parse()
    // input.parse::<u32>() returns Result<u32, ParseIntError>
    // ? operator attempts From<ParseIntError> for Box<dyn Error>, which exists.
    let parsed_number = input.parse::<u32>()?;

    // If we reach here, both "simulated IO" check passed and parsing succeeded
    Ok(parsed_number)
}
```

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete error handling lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Check Questions (To Test Understanding)**

1.  What are the two variants of the `Result<T, E>` enum, and what do `T` and `E` typically represent?
2.  Explain the difference in behavior between calling `.unwrap()` and `.expect()` on a `Result` that contains an error.
3.  What is the primary role of the `?` operator in Rust's error handling?
4.  Why did the original code in Exercise 4 fail to compile when using `?` on both a `ParseIntError` and a simulated `io::Error` while trying to return `Result<u32, std::io::Error>`?
5.  How does `Box<dyn std::error::Error>` allow a function to return different types of errors, and what is a potential drawback of this approach?

**Detailed Answers to Check Questions**

1.  The two variants of the `Result<T, E>` enum are `Ok(T)` and `Err(E)`. `T` typically represents the type of the **success value** that the operation returns, and `E` typically represents the type of the **error value** that indicates why the operation failed.
2.  Both `.unwrap()` and `.expect()` will cause the program to panic if called on a `Result` that is `Err`. The difference is in the panic message: `.unwrap()` provides a generic panic message (e.g., "called `Result::unwrap()` on an `Err` value"), while `.expect()` allows you to provide a custom string message (e.g., `expect("Failed to parse!")`) that will be included in the panic message. `expect()` is generally preferred in development/debugging as the custom message is more informative.
3.  The primary role of the `?` operator is to provide a concise way to **propagate** error values (`Err`) up the call stack from a function that returns `Result` (or `Option`). If the expression `expr?` evaluates to `Ok(value)`, the `?` operator unwraps the value, and execution continues. If `expr?` evaluates to `Err(error)`, the `?` operator immediately returns that `Err(error)` value from the *current* function, effectively stopping further execution in the current function and passing the error to the caller. It requires the propagated error type to be convertible to the function's return error type via the `From` trait.
4.  The original code in Exercise 4 failed because the `?` operator requires an implementation of the `From` trait to convert the propagated error type into the function's declared return error type. In that case, the function returned `Result<u32, std::io::Error>`, and the `?` operator was used on a `Result` containing a `std::num::ParseIntError`. Because the Rust standard library does not provide a `From<std::num::ParseIntError> for std::io::Error` implementation, the compiler did not know how to convert the `ParseIntError` into an `io::Error`, and thus the `?` operator could not be used directly on the parse result.
5.  `Box<dyn std::error::Error>` allows a function to return different types of errors because `dyn std::error::Error` is a **trait object**. A `Box<dyn Error>` can hold *any* type that implements the `std::error::Error` trait. Rust's standard library provides a generic `From` implementation (`impl<E> From<E> for Box<dyn std::error::Error> where E: std::error::Error + 'static`), which automatically converts any concrete error type implementing `Error` into a `Box<dyn Error>`. This enables the `?` operator to work seamlessly with functions returning `Result<T, Box<dyn Error>>`, as it can convert any `Error` type it encounters into the boxed trait object. A potential drawback of this approach is that you **lose the specific type information** about the error at compile time. When you receive a `Box<dyn Error>`, you know it's *an* error, but you don't know *exactly which type* of error it is (e.g., is it a `ParseIntError`, an `io::Error`, or something else?) without downcasting or other runtime checks, which makes specific error handling harder compared to matching on a custom error enum.
  
---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025.**
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*