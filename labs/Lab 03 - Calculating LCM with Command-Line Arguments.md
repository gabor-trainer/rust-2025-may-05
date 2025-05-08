# Lab 03: Calculating LCM with Command-Line Arguments

## 2. Introduction & Goal

This lab focuses on building a command-line utility in Rust that calculates the Least Common Multiple (LCM) for a series of positive integers provided as arguments. It will reinforce skills in handling command-line input, basic number theory concepts, and iterative calculation within a Rust program structure.

**Primary Learning Objective:** To practice reading and parsing numerical command-line arguments, implementing a common mathematical algorithm (LCM, dependent on GCD), and structuring a basic executable Rust program that processes input from the user.

**Concrete Outcomes:** By the end of this lab, you will be able to:

*   Access and process multiple command-line arguments.
*   Parse strings from arguments into numerical types (`u64`).
*   Implement the Euclidean algorithm for calculating the Greatest Common Divisor (GCD).
*   Implement a function to calculate the Least Common Multiple (LCM) using its relationship with GCD.
*   Iteratively calculate the LCM for more than two numbers.
*   Handle basic errors during argument parsing.

## 3. Scenario

In various computational tasks, especially those involving cycles, periods, or scheduling, finding the Least Common Multiple of a set of numbers is a fundamental requirement. For example, determining when several events that repeat at different intervals will next occur simultaneously. Building a simple command-line tool allows you to quickly compute the LCM for any set of numbers without needing a specialized calculator or software.

*Why use Rust for this?* Rust's performance and reliability make it well-suited for numerical computations and command-line utilities. Its strong type system helps ensure that you handle numbers correctly, and its standard library provides the necessary tools for argument processing. Implementing this classic algorithm provides practical experience with basic Rust programming patterns.

## 4. Prerequisites

*   **Software:**
    *   **Rust Toolchain:** Installed (`rustc`, `cargo`). This lab is based on Rust `edition = "2024"`. We assume the following tool versions: `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`. Ensure you have at least Rust 1.56.0 or later.
    *   **A Text Editor or IDE:** Such as VS Code with `rust-analyzer`, Sublime Text, Vim, Emacs, etc.
    *   **Access to a Terminal or Command Prompt.**
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, loops, `if`, `match`).
    *   Understanding of primitive types like `u64`.
    *   Familiarity with `Vec` (vectors) for storing collections of data.
    *   Understanding of `Result` and `Option` enums for handling potential failures.
    *   Basic understanding of `Cargo.toml`.
    *   Basic command-line usage (running commands with arguments).
    *   Basic mathematical understanding of Greatest Common Divisor (GCD) and Least Common Multiple (LCM).
*   **Setup Verification:** Open your terminal and confirm your Rust installation by running:
    ```bash
    rustc --version
    cargo --version
    ```
    Verify the versions displayed. If Rust is not found or the versions are significantly older, consider updating via `rustup update` or following the installation guide: [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install).

## 5. Setup Instructions

1.  **Navigate to your repository and branch:** Before creating the project, ensure you are in your training repository's root directory and on the correct Git branch for this lab. Check for any uncommitted changes from previous work.
2.  **Create a New Rust Project:**
    Open your terminal, navigate to the directory within your repository where you organize your lab projects, and run:
    ```bash
    cargo new lcm_calculator --edition 2024
    ```
    This command creates a new `lcm_calculator` directory with the necessary basic files (`Cargo.toml`, `src/main.rs`). We are using the `2024` edition of Rust, which includes various improvements. Rust editions (2015, 2018, 2021, 2024) are a mechanism for evolving the language in a backwards-compatible way.
3.  **Remove the generated Git repository:** `cargo new` initializes a Git repository by default. Since you are likely working within an existing monorepo for this training, this nested `.git` folder inside `lcm_calculator` is unnecessary and should be removed to prevent conflicts or confusion with your main repository's version control.
    ```bash
    # On Linux/macOS/Git Bash
    rm -rf lcm_calculator/.git

    # On Windows Command Prompt
    rmdir /s /q lcm_calculator\.git

    # On Windows PowerShell
    Remove-Item -Recurse -Force lcm_calculator\.git
    ```
4.  **Navigate into the project directory:**
    ```bash
    cd lcm_calculator
    ```
5.  No external dependencies are required for this project, as GCD and LCM calculation will use standard library features and basic arithmetic.
6.  **Commit often:** As you implement the steps of this lab, commit your changes frequently using `git commit`. This saves your progress and creates useful checkpoints. Consider using [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `feat(lcm): initial project setup`, `feat(lcm): implemented gcd function`) for a clear commit history.

## 6. Lab Steps (Iterative Development)

Now, let's build the program step by step by modifying the `src/main.rs` file. Open `src/main.rs` in your text editor.

*   **Step 1: Initial Structure and Argument Parsing**
    *   **Task:** Set up the basic `main` function structure, including accessing and parsing command-line arguments into numbers.
    *   **Code Implementation:** Replace the *entire* content of `src/main.rs` with the following code:
        ```rust
        // In src/main.rs

        use std::str::FromStr; // To parse strings into numbers
        use std::env; // To access command-line arguments
        use std::process; // To exit the program gracefully
        use std::io::Write; // To use stderr().flush() (for printing error messages)

        fn main() {
            let mut numbers = Vec::new(); // 1

            for arg in env::args().skip(1) { // 2
                numbers.push(u64::from_str(&arg) // 3
                    .expect("error: invalid argument provided - not a positive integer")); // 4
            }

            if numbers.len() == 0 { // 5
                eprintln!("Usage: lcm_calculator NUMBER ..."); // 6
                process::exit(1); // 7
            }

            // We will calculate LCM starting with the first number
            let mut result_lcm = numbers[0]; // 8

            // The loop to calculate LCM will go here later

            // Final output (will be updated for LCM)
            println!("The LCM of {:?} is {}", numbers, result_lcm); // 9
        }

        // We will add gcd and lcm functions later
        ```
    *   **Explanation:**
        *   `use std::str::FromStr;`: Imports the `FromStr` trait, which provides the `from_str` method used to convert strings into other types (like `u64`).
        *   `use std::env;`, `use std::process;`, `use std::io::Write;`: Imports necessary modules for arguments, exiting, and ensuring error messages are flushed immediately.
        *   `let mut numbers = Vec::new();` (1): Initializes an empty, mutable vector named `numbers` to store the parsed unsigned 64-bit integers (`u64`).
        *   `for arg in env::args().skip(1) { ... }` (2): This loop iterates over the command-line arguments. `env::args()` returns an iterator including the program name. `.skip(1)` skips the program name, leaving only the arguments provided by the user. Each argument is a `String`.
        *   `u64::from_str(&arg)` (3): Inside the loop, `u64::from_str` attempts to parse the current argument (`arg`, passed as a string slice `&str`) into a `u64`. This returns a `Result<u64, ParseIntError>`, as parsing might fail (e.g., if the argument is "hello" or "-5").
        *   `.expect("...")` (4): The `.expect()` method is called on the `Result`. If the `Result` is `Ok(value)`, it unwraps and returns the `value` (the parsed `u64`). If the `Result` is `Err(error)`, it causes the program to `panic!` with the provided error message. While simple for now, we'll see a more robust way to handle parsing errors later.
        *   `numbers.push(...)`: The successfully parsed `u64` is added to the `numbers` vector.
        *   `if numbers.len() == 0 { ... }` (5): After the loop, this checks if any valid numbers were successfully parsed. If the user ran the program without any arguments (after skipping the program name), the `numbers` vector will be empty.
        *   `eprintln!("Usage: ...")` (6): If no numbers were provided, this prints a usage message to standard error (`eprintln!`). Note the program name is hardcoded here for simplicity, unlike the previous lab.
        *   `process::exit(1);` (7): Exits the program with a non-zero status code (conventionally 1) to indicate an error.
        *   `let mut result_lcm = numbers[0];` (8): If there are numbers, we initialize our variable `result_lcm` with the first number in the vector. This will be the starting point for our iterative LCM calculation.
        *   `println!("...")` (9): This line prints the final result. We use the debug format specifier `{:?}` to print the entire `numbers` vector and the standard `{}` for the calculated `result_lcm`.
    *   **Verification:** Save `src/main.rs`. Open your terminal in the `lcm_calculator` directory and run `cargo check`. It should compile without errors. Now, test running the program with different inputs:
        ```bash
        cargo run
        ```
        Expected Output: Usage message on `stderr`, program exits with code 1.
        ```bash
        cargo run abc def
        ```
        Expected Output: Panic message "error: invalid argument provided..." on `stderr`, program exits.
        ```bash
        cargo run 10 20 30
        ```
        Expected Output: `The LCM of [10, 20, 30] is 10`. (The result is 10 because we haven't implemented the LCM calculation loop yet, it's just printing the first number). This confirms argument parsing is working for valid numbers.

*   **Step 2: Implement the GCD Function**
    *   **Task:** Implement the Greatest Common Divisor (GCD) function, which is a necessary building block for calculating the LCM. We will use the Euclidean algorithm.
    *   **Code Implementation:** Add the following function *above* the `main` function in `src/main.rs`:
        ```rust
        // In src/main.rs, add above the main function:

        fn gcd(mut a: u64, mut b: u64) -> u64 { // 1
            // The Euclidean Algorithm
            while b != 0 { // 2
                if b < a { // 3
                    let temp = b;
                    b = a;
                    a = temp;
                }
                b = b % a; // 4
            }
            a // 5
        }

        // Keep main function below this
        // fn main() { ... }
        ```
    *   **Explanation:**
        *   `fn gcd(mut a: u64, mut b: u64) -> u64`: Defines a function `gcd` that takes two mutable `u64` numbers (`mut a`, `mut b`) and returns a single `u64` (their GCD). We use `mut` because the algorithm modifies the values of `a` and `b` within the function.
        *   `while b != 0 { ... }` (2): The Euclidean algorithm continues as long as the second number (`b`) is not zero.
        *   `if b < a { ... }` (3): This block ensures that `a` is always less than or equal to `b` before the modulo operation. Swapping them simplifies the logic (`b = b % a` then becomes `b = b % original_a` if `b > a`). While not strictly necessary for correctness, it's common in this implementation.
        *   `b = b % a;` (4): This is the core step: replace `b` with the remainder of `b` divided by `a`. The GCD of `a` and `b` is the same as the GCD of `a` and `b % a`.
        *   `a` (5): When the loop terminates (i.e., `b` becomes 0), `a` holds the greatest common divisor of the original two numbers.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. To verify the `gcd` function logic, you can temporarily add a call to it in `main`:
        ```rust
        // In main function, after the `if numbers.len() == 0` block:
        // println!("GCD(12, 18) is {}", gcd(12, 18)); // Temp test
        // println!("GCD(10, 25) is {}", gcd(10, 25)); // Temp test
        // println!("GCD(7, 7) is {}", gcd(7, 7)); // Temp test
        ```
        Run `cargo run 10 20` (the numbers don't matter for this temp test). The output should show `GCD(12, 18) is 6`, `GCD(10, 25) is 5`, and `GCD(7, 7) is 7`. Remove or comment out these temporary `println!` calls once you're satisfied.

*   **Step 3: Implement the LCM Function**
    *   **Task:** Implement the Least Common Multiple (LCM) function. The relationship between LCM and GCD for two positive integers `a` and `b` is `LCM(a, b) = (a * b) / GCD(a, b)`. A safer way to calculate this for potentially large numbers is `LCM(a, b) = (a / GCD(a, b)) * b` to prevent intermediate overflow.
    *   **Code Implementation:** Add the following function *above* the `main` function (but *below* the `gcd` function) in `src/main.rs`:
        ```rust
        // In src/main.rs, add below the gcd function:

        fn lcm(a: u64, b: u64) -> u64 { // 1
            if a == 0 || b == 0 { // 2
                return 0; // The LCM of any number and 0 is 0
            }
            // Using the formula: LCM(a, b) = |a * b| / GCD(a, b)
            // To prevent potential overflow with large numbers, we can calculate as (a / GCD(a, b)) * b
            (a / gcd(a, b)) * b // 3
        }

        // Keep main function below this
        // fn main() { ... }
        ```
    *   **Explanation:**
        *   `fn lcm(a: u64, b: u64) -> u64`: Defines the `lcm` function taking two `u64`s and returning their LCM as a `u64`.
        *   `if a == 0 || b == 0 { return 0; }` (2): Handles the edge case where one or both inputs are 0. The LCM of any positive number and 0 is defined as 0. The GCD function handles 0 inputs correctly (e.g., `gcd(5, 0)` is 5), but calculating `(5 * 0) / 5` using the standard formula would work, while `(5 / gcd(5,0)) * 0` also works. However, explicitly returning 0 is clearer and handles `lcm(0, 0)` which should also be 0.
        *   `(a / gcd(a, b)) * b` (3): This calculates the LCM. We first find the `gcd` of `a` and `b`. Then we divide `a` by the GCD *before* multiplying by `b`. This helps prevent potential integer overflow if the product `a * b` would exceed the maximum value of `u64`, even if the final LCM result fits within `u64`. Since GCD divides both `a` and `b`, `a / gcd(a, b)` is always an integer.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. Temporarily test the `lcm` function in `main` (after the `if numbers.len() == 0` block):
        ```rust
        // In main function:
        // println!("LCM(10, 20) is {}", lcm(10, 20)); // Temp test (Expected: 20)
        // println!("LCM(12, 18) is {}", lcm(12, 18)); // Temp test (Expected: 36)
        // println!("LCM(7, 7) is {}", lcm(7, 7));   // Temp test (Expected: 7)
        // println!("LCM(0, 5) is {}", lcm(0, 5));   // Temp test (Expected: 0)
        ```
        Run `cargo run 1 2`. The output should show the expected LCM results from your test calls. Remove or comment out these temporary `println!` calls.

*   **Step 4: Integrate LCM Calculation into Main Loop**
    *   **Task:** Modify the `main` function's loop to iteratively calculate the LCM of all provided numbers. The LCM of multiple numbers `n1, n2, n3, ...` is `LCM(n1, LCM(n2, LCM(n3, ...)))`.
    *   **Code Implementation:** Modify the loop in `main` to use the `lcm` function:
        ```rust
        // In src/main.rs, inside the main function:

        fn main() {
            let mut numbers = Vec::new();

            for arg in env::args().skip(1) {
                numbers.push(u64::from_str(&arg)
                    .expect("error: invalid argument provided - not a positive integer"));
            }

            if numbers.len() == 0 {
                eprintln!("Usage: lcm_calculator NUMBER ...");
                process::exit(1);
            }

            let mut result_lcm = numbers[0]; // Starts with the first number

            // Loop through the rest of the numbers, calculating the LCM cumulatively
            for m in &numbers[1..] { // 1
                result_lcm = lcm(result_lcm, *m); // 2
            }

            // Final output message
            println!("The LCM of {:?} is {}", numbers, result_lcm); // 3
        }

        // ... gcd and lcm functions below ...
        ```
    *   **Explanation:**
        *   `for m in &numbers[1..]` (1): This loop iterates over the elements of the `numbers` vector starting from the *second* element (`[1..]` is a slice representing elements from index 1 to the end). We iterate over references (`&numbers`) and get a reference `&m` to each number in the slice.
        *   `result_lcm = lcm(result_lcm, *m);` (2): Inside the loop, we update `result_lcm`. We calculate the LCM of the current `result_lcm` (which holds the LCM of the numbers processed so far) and the current number `*m` (we use `*` to dereference the reference `&m` to get the `u64` value). The result becomes the new `result_lcm`. This process correctly computes the LCM of all numbers: `lcm(n1, n2, n3) = lcm(lcm(n1, n2), n3)`.
        *   `println!("...")` (3): The final `println!` now uses the correctly calculated `result_lcm`.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. Now, run the program with several numbers and verify the output:
        ```bash
        cargo run 10 12 15
        ```
        Expected Output: `The LCM of [10, 12, 15] is 60`. (LCM(10, 12) = 60, LCM(60, 15) = 60).
        ```bash
        cargo run 7 13
        ```
        Expected Output: `The LCM of [7, 13] is 91`. (7 and 13 are prime, LCM is their product).
        ```bash
        cargo run 14 21 35
        ```
        Expected Output: `The LCM of [14, 21, 35] is 210`. (LCM(14, 21)=42, LCM(42, 35)=210).
        ```bash
        cargo run 5
        ```
        Expected Output: `The LCM of [5] is 5`. (The loop from `[1..]` won't run if there's only one number, which is correct).
        ```bash
        cargo run 0 5 10
        ```
        Expected Output: `The LCM of [0, 5, 10] is 0`. (Our `lcm` function handles the 0 input correctly).

*   **Step 5 (Optional Refinement): Graceful Error Handling for Parsing**
    *   **Task:** Replace the `.expect()` call during argument parsing with explicit `Result` handling to provide more user-friendly error messages without crashing the program with a panic.
    *   **Code Implementation:** Modify the argument parsing loop in `main`:
        ```rust
        // In src/main.rs, inside the main function:

        fn main() {
            let mut numbers = Vec::new();
            let mut errors_found = false; // Flag to track if any parsing errors occurred

            for arg in env::args().skip(1) {
                match u64::from_str(&arg) { // 1
                    Ok(number) => {
                        numbers.push(number);
                    }
                    Err(_) => { // 2
                        eprintln!("error: invalid argument '{}' - not a positive integer", arg);
                        errors_found = true; // Mark that an error occurred
                    }
                }
            }

            if errors_found { // 3
                 // Note: We exit *after* checking all arguments,
                 // so the user sees all parsing errors at once.
                process::exit(1);
            }

            if numbers.len() == 0 {
                eprintln!("Usage: lcm_calculator NUMBER ...");
                // Note: We don't exit here if errors_found is true,
                // as the errors_found check handled it.
                // If errors_found is false, this means *no* args were provided,
                // which is still an error. We might reach here
                // if args were all invalid, handled by errors_found check.
                // Let's refine the exit logic slightly.
                eprintln!("No valid numbers provided."); // Refined message
                process::exit(1);
            }


            let mut result_lcm = numbers[0];

            for m in &numbers[1..] {
                result_lcm = lcm(result_lcm, *m);
            }

            println!("The LCM of {:?} is {}", numbers, result_lcm);
        }

        // ... gcd and lcm functions below ...
        ```
    *   **Explanation:**
        *   `match u64::from_str(&arg) { ... }` (1): Instead of `.expect()`, we use a `match` statement on the `Result` returned by `from_str`.
        *   `Ok(number) => { numbers.push(number); }`: If parsing succeeds (`Ok`), we unwrap the number and add it to the `numbers` vector as before.
        *   `Err(_) => { ... }` (2): If parsing fails (`Err`), we print a specific error message to standard error (`eprintln!`) indicating *which* argument failed. We use `_` to ignore the specific `ParseIntError` details for simplicity in the user-facing message, although we could print `e` like in the previous lab's file I/O errors if we wanted more verbosity. We also set the `errors_found` flag to `true`.
        *   `if errors_found { ... }` (3): After the loop finishes checking *all* arguments, we check the `errors_found` flag. If it's `true`, it means at least one argument was invalid. We print an informative message and then exit with status 1. This allows the program to report *all* parsing errors in the arguments provided, rather than stopping at the first one like `.expect()` would.
        *   Refinement to the `numbers.len() == 0` check: This check now captures the case where *no* arguments were provided, or *all* provided arguments were invalid (since invalid ones are not added to `numbers`). The message is updated to reflect that no *valid* numbers were found.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. Test with mixed valid and invalid inputs:
        ```bash
        cargo run 10 abc 20 def 30
        ```
        Expected Output: Error messages for 'abc' and 'def', followed by a message that no valid numbers were provided, exiting with status 1.
        ```bash
        cargo run 10 20 30
        ```
        Expected Output: Still calculates LCM correctly (no errors should be printed).

## 7. Running the Application / Testing

To run the completed application, navigate to the `lcm_calculator` directory in your terminal and use the `cargo run` command followed by one or more positive integers as arguments.

**Example:**

To calculate the LCM of 6, 8, and 12:

```bash
cargo run 6 8 12
```

**Expected Output:**

```text
The LCM of [6, 8, 12] is 24
```

**Testing with Different Inputs:**

*   Provide a single number (`cargo run 42`). LCM of a single number is the number itself.
*   Provide two numbers (`cargo run 100 150`). LCM(100, 150) = 300.
*   Provide multiple numbers (`cargo run 2 3 4 5`). LCM(2,3,4,5) = 60.
*   Include 0 as an input (`cargo run 0 10 20`). Expected LCM is 0.
*   Test error cases: Run with no arguments (`cargo run`), with non-numeric arguments (`cargo run 10 abc 20`). Observe the specific error messages printed to standard error and the program's exit behavior.

## 8. Key Concepts Review

*   **Command-Line Arguments (`std::env`)**: Accessing arguments via `env::args()` and using `skip(1)` to get user-provided arguments.
*   **Parsing Strings to Numbers (`std::str::FromStr`, `u64::from_str`)**: Converting string arguments into unsigned 64-bit integers (`u64`).
*   **Vector (`Vec`)**: Using `Vec<u64>` to store a dynamic list of the parsed numbers.
*   **Loops (`for`, `while`)**: Iterating over arguments and implementing algorithms like GCD and the iterative LCM calculation.
*   **Functions**: Defining reusable blocks of code for `gcd` and `lcm`.
*   **Mutability (`mut`)**: Using `mut` with variables (`mut a`, `mut b` in `gcd`, `mut numbers`, `mut result_lcm`) and vector elements (`*m`) when their values need to be changed.
*   **Borrowing (`&`) and Dereferencing (`*`)**: Iterating over references in a slice (`&numbers[1..]`) and using `*` to get the value from the reference.
*   **Error Handling (`Result`, `match`)**: Using `match` to handle the success (`Ok`) or failure (`Err`) result of parsing strings (`u64::from_str`).
*   **Explicit Error Reporting (`eprintln!`)**: Printing error messages to standard error.
*   **Program Exit (`std::process::exit`)**: Controlling the program's exit status.
*   **Euclidean Algorithm**: The classic method implemented in `gcd` to find the greatest common divisor.
*   **LCM Formula**: Using the relationship `LCM(a, b) = (a / GCD(a, b)) * b` to calculate the least common multiple of two numbers while mitigating overflow risk.
*   **Iterative LCM Calculation**: Computing the LCM of multiple numbers by repeatedly applying the pairwise LCM function: `LCM(n1, n2, ..., nk) = LCM(LCM(n1, n2), ..., nk)`.

## 9. (Optional) Challenges / Next Steps

1.  **Use `clap` for Arguments:** Replace the manual argument parsing and error handling in `main` with the `clap` crate ([https://crates.io/crates/clap](https://crates.io/crates/clap)). This will automatically generate usage messages and handle invalid arguments more robustly.
2.  **Handle Negative or Zero Input More Strictly:** Currently, we parse into `u64` (unsigned, non-negative). If you wanted to support *any* integers, you'd need `i64` and adjust the GCD/LCM logic or handle non-positive inputs as errors (except for the specific `lcm(x, 0) = 0` case). Refine the error messages to be more specific (e.g., distinguish non-numeric from negative).
3.  **Handle Potential Overflow in `lcm` Result:** Although `u64` is large, it's possible for the LCM of many large numbers to exceed `u64::MAX`. Modify the `lcm` function to check for potential overflow *before* the multiplication `(a / gcd(a, b)) * b`. If overflow would occur, perhaps return a `Result<u64, YourOverflowError>` or print a warning. The `checked_mul()` method on integers can help here.
4.  **Custom Error Type:** Like in the previous lab, define a custom error `enum` (perhaps using `thiserror`) to represent different kinds of errors (e.g., `NoNumbers`, `InvalidNumber(String, ParseIntError)`) and return a `Result` from `main`.

## 10. (Optional) Troubleshooting

*   **`error: could not find `Cargo.toml`...`**: Ensure your terminal's current directory is the `lcm_calculator` project root (`cd lcm_calculator`) when running `cargo` commands.
*   **Compile Errors (`cannot find function`, `expected struct Vec<u64>`, etc.)**: Double-check your `use` statements at the top of `src/main.rs`. Verify variable names and types match the code examples (`u64`, `Vec<u64>`, `&u64`).
*   **Panic Message `error: invalid argument provided...`**: This means the `.expect()` call in the parsing loop failed because one of the command-line arguments could not be converted to a positive integer (`u64`). Rerun with only valid integer arguments. (This will be replaced by a more graceful error if you implement Step 5).
*   **Incorrect LCM Result**:
    *   Verify the `gcd` function is implemented correctly using the Euclidean algorithm. Test it with known pairs (e.g., 12, 18 -> 6).
    *   Verify the `lcm` function implements the formula `(a / gcd(a, b)) * b` and handles the zero case. Test it with known pairs (e.g., 10, 20 -> 20; 12, 18 -> 36).
    *   Verify the loop in `main` correctly iterates over the numbers from the second one onwards and accumulates the LCM using `result_lcm = lcm(result_lcm, *m);`.
*   **Integer Overflow**: If you encounter a runtime panic message like "attempt to multiply with overflow", this means the result of `(a / gcd(a, b)) * b` in the `lcm` function exceeded `u64::MAX` for some pair of numbers during the iterative calculation. This is less likely with `u64` unless you provide extremely large inputs, but it's a possibility. Consider implementing Challenge 3.

## 11. Conclusion

Fantastic work! You've successfully built a functional command-line utility in Rust to calculate the Least Common Multiple. This lab provided hands-on practice with crucial command-line application patterns, including parsing arguments, handling basic input errors, and implementing core mathematical logic. You reinforced your understanding of Rust's type system, basic control flow, functions, and the iterative approach to problem-solving. This is a solid step towards building more complex and robust CLI tools.

Remember to commit the final state of your project to your Git repository using `git commit` and push your changes (`git push`). Adopting Conventional Commits will help keep your project history clean and easy to navigate.

## 12. Final Solution Code

Here is the complete code for `src/main.rs` after completing all the steps, including the optional Step 5 for more graceful error handling:

```rust
// Final Code for src/main.rs

use std::str::FromStr; // To parse strings into numbers
use std::env; // To access command-line arguments
use std::process; // To exit the program gracefully
use std::io::Write; // To use stderr().flush() (for printing error messages)

// Function to calculate the Greatest Common Divisor (GCD)
// using the Euclidean algorithm.
fn gcd(mut a: u64, mut b: u64) -> u64 {
    // Handle the case where both inputs are 0, GCD is usually undefined
    // or considered 0 in some contexts. Our while loop handles one 0 correctly.
    // Let's assume positive integers as per prompt, but be robust.
    // While b is not zero...
    while b != 0 {
        // If b is smaller than a, swap them.
        // This simplifies the modulo operation logic.
        if b < a {
            let temp = b;
            b = a;
            a = temp;
        }
        // Replace b with the remainder of b divided by a.
        b = b % a;
    }
    // When b becomes 0, a holds the GCD.
    a
}

// Function to calculate the Least Common Multiple (LCM)
fn lcm(a: u64, b: u64) -> u64 {
    // The LCM of any number and 0 is 0.
    if a == 0 || b == 0 {
        return 0;
    }
    // Using the formula: LCM(a, b) = |a * b| / GCD(a, b)
    // Calculating as (a / GCD(a, b)) * b to prevent potential overflow
    // if a * b is very large, but the final LCM fits in u64.
    // Note: This division should not panic if a > 0 and gcd(a,b) is calculated correctly.
    (a / gcd(a, b)) * b
}


fn main() {
    let mut numbers = Vec::new();
    let mut errors_found = false; // Flag to track if any parsing errors occurred

    // Iterate over command-line arguments, skipping the program name
    for arg in env::args().skip(1) {
        // Attempt to parse each argument as a u64
        match u64::from_str(&arg) {
            Ok(number) => {
                // If successful, add the number to our vector
                numbers.push(number);
            }
            Err(_) => {
                // If parsing fails, print an error message to standard error
                eprintln!("error: invalid argument '{}' - not a positive integer", arg);
                errors_found = true; // Mark that an error occurred
            }
        }
    }

    // If any parsing errors were found, print an additional message and exit
    if errors_found {
        eprintln!("Please provide only positive integer arguments.");
        process::exit(1);
    }

    // Check if any valid numbers were provided
    if numbers.len() == 0 {
        eprintln!("Usage: lcm_calculator NUMBER ...");
        eprintln!("No valid numbers provided.");
        process::exit(1);
    }

    // Initialize the result with the first number
    let mut result_lcm = numbers[0];

    // Iterate through the rest of the numbers and calculate the cumulative LCM
    for m in &numbers[1..] {
        result_lcm = lcm(result_lcm, *m);
    }

    // Print the final result to standard output
    println!("The LCM of {:?} is {}", numbers, result_lcm);
}
```

Final code for `lcm_calculator/Cargo.toml`:

```toml
# Final Code for lcm_calculator/Cargo.toml

[package]
name = "lcm_calculator"
version = "0.1.0"
edition = "2024" # Using the 2024 edition

# See more keys and their definitions at
# https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
# No external dependencies needed for this lab
```

## 13. Check Questions (To Test Understanding)

1.  In the `main` function, why do we call `.skip(1)` on the result of `env::args()`?
2.  What is the difference in how parsing errors are handled by `.expect()` (used in Step 1) versus the `match` statement with `u64::from_str()` (used in Step 5)? Why is the `match` approach generally preferred for user input?
3.  Briefly explain the mathematical relationship between the Greatest Common Divisor (GCD) and the Least Common Multiple (LCM) of two positive integers, and how this relationship is used in the `lcm` function.
4.  The `main` function calculates the LCM of multiple numbers iteratively in a loop (`result_lcm = lcm(result_lcm, *m)`). Explain how this iterative process correctly arrives at the LCM of the entire set of numbers.
5.  Consider the `lcm` function. Why is the calculation `(a / gcd(a, b)) * b` used instead of the more intuitive `(a * b) / gcd(a, b)`?

## 14. Detailed Answers to Check Questions

1.  `env::args()` returns an iterator where the *first* element is the path or name of the executable program itself (e.g., `"./target/debug/lcm_calculator"`). The user-provided arguments start from the *second* element. Calling `.skip(1)` discards this first element, allowing the loop to process only the arguments that the user typed after the program name.
2.  When `u64::from_str()` returns an `Err`, the `.expect()` method causes the program to immediately **panic!**. This stops the program's execution abruptly and prints a panic message (including the one you provide). The `match` statement, however, allows you to **explicitly handle** the `Ok` and `Err` cases. In the `Err` case, you can execute specific code, such as printing a user-friendly error message (`eprintln!`) without crashing, and then decide whether or not to exit the program based on your logic (like our `errors_found` flag). The `match` approach is preferred for user input because it allows for more graceful error handling, providing informative messages to the user and potentially collecting multiple errors before exiting, rather than just crashing on the first problem.
3.  For any two positive integers `a` and `b`, the product of their GCD and LCM is equal to the product of the numbers themselves: `GCD(a, b) * LCM(a, b) = a * b`. Therefore, the LCM can be calculated as `LCM(a, b) = (a * b) / GCD(a, b)`. The `lcm` function uses this relationship by calculating the GCD and then performing division and multiplication based on this formula.
4.  The LCM of a set of numbers can be found by repeatedly calculating the LCM of pairs. `LCM(n1, n2, n3) = LCM(LCM(n1, n2), n3)`. The loop in `main` implements this by initializing `result_lcm` with the first number (`n1`). It then iterates through the rest of the numbers (`n2`, `n3`, ...). In each iteration, it updates `result_lcm` to be the LCM of its current value (the LCM of numbers processed so far) and the next number from the input list. After the loop finishes, `result_lcm` holds the LCM of all the numbers in the input vector.
5.  The calculation `(a / gcd(a, b)) * b` is used instead of `(a * b) / gcd(a, b)` primarily to **mitigate the risk of integer overflow** when dealing with potentially large numbers. The product `a * b` could exceed the maximum value that `u64` can hold, even if the final LCM result fits within `u64`. By dividing `a` by `gcd(a, b)` first (which is guaranteed to result in an integer because GCD is a divisor of `a`), we perform a smaller multiplication step `(result of division) * b`, making it less likely to overflow before reaching the final LCM value.

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025.**
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*