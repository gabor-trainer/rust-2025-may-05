# Lab 04: Building a CLI Tool with `std`, `regex`, and `text-colorizer`

## 2. Introduction & Goal

In this lab, you will build a practical command-line utility in Rust that performs a find-and-replace operation within a text file. This will involve reading from a file, processing text, writing to another file, handling command-line arguments, and managing potential errors along the way. We will leverage several standard library components and two useful external crates (`regex` and `text-colorizer`) to accomplish this.

**Primary Learning Objective:** To gain hands-on experience building a functional command-line application in Rust, focusing on input/output (`std::fs`), command-line argument parsing (`std::env`), using external libraries, basic error handling (`Result`, `match`), and text processing with regular expressions.

**Concrete Outcomes:** By the end of this lab, you will be able to:

*   Define a struct to hold command-line arguments.
*   Parse command-line arguments provided to your Rust program.
*   Read the entire content of a file into a string.
*   Use the `regex` crate to find and replace text patterns.
*   Write string data to a new file.
*   Implement basic error checking for file operations and argument parsing.
*   Incorporate external crates into your project using `Cargo.toml`.
*   Use the `text-colorizer` crate to add color to your terminal output.

## 3. Scenario

You frequently encounter situations where you need to quickly replace all occurrences of a specific word or pattern in a text file with something else. While general-purpose editors or tools exist, building a dedicated command-line utility in Rust for this task allows you to create a highly efficient and reliable solution. This utility will read from an input file, perform the replacement, and write the result to an output file, providing clear feedback or error messages to the user via the console, potentially with color highlighting.

*Why use Rust for this?* Rust is an excellent choice for command-line tools due to its focus on performance, memory safety without a garbage collector, and robust error handling capabilities. The `regex` crate provides a powerful and fast regular expression engine, and `cargo` makes managing dependencies like `text-colorizer` straightforward. Building this tool will demonstrate how these Rust features come together for practical applications.

## 4. Prerequisites

*   **Software:**
    *   **Rust Toolchain:** Installed (`rustc`, `cargo`). This lab is based on Rust `edition = "2024"`. We assume the following tool versions: `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`. Ensure you have at least Rust 1.56.0 or later.
    *   **A Text Editor or IDE:** Such as VS Code with `rust-analyzer`, Sublime Text, Vim, Emacs, etc.
    *   **Access to a Terminal or Command Prompt.**
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `if`, `match`).
    *   Understanding of structs and enums (specifically `Result` and `Option`).
    *   Familiarity with `Cargo.toml` for project configuration.
    *   Basic understanding of ownership and borrowing (`&str`).
    *   Basic command-line usage (navigating directories, running commands with arguments).
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
    cargo new quickreplace --edition 2024
    ```
    This command creates a new `quickreplace` directory with the necessary basic files (`Cargo.toml`, `src/main.rs`). We are using the `2024` edition of Rust, which includes various improvements. Rust editions (2015, 2018, 2021, 2024) provide opt-in ways to use newer language features.
3.  **Remove the generated Git repository:** `cargo new` initializes a Git repository by default. Since you are likely working within an existing monorepo for this training, this nested `.git` folder inside `quickreplace` is unnecessary and should be removed to prevent conflicts or confusion with your main repository's version control.
    ```bash
    # On Linux/macOS/Git Bash
    rm -rf quickreplace/.git

    # On Windows Command Prompt
    rmdir /s /q quickreplace\.git

    # On Windows PowerShell
    Remove-Item -Recurse -Force quickreplace\.git
    ```
4.  **Navigate into the project directory:**
    ```bash
    cd quickreplace
    ```
5.  **Add Dependencies:** We need to add the `regex` and `text-colorizer` crates to our project. Open the `Cargo.toml` file in your text editor. Find the `[dependencies]` section and add the following lines:
    ```toml
    # In quickreplace/Cargo.toml

    [dependencies]
    text-colorizer = "1"
    regex = "1"

    # Add these lines below the existing [dependencies] heading.
    ```
    Alternatively, you can use the `cargo add` command (available if you have `cargo-edit` installed, usually via `cargo install cargo-edit`):
    ```bash
    cargo add text-colorizer regex
    ```
    This tells Cargo to fetch these crates from crates.io (the official Rust package registry) and make them available to our project. The `"1"` specifies that we accept any version compatible with `1.0.0`.
6.  **Create Sample Input File:** Create a new file named `input.txt` directly in the `quickreplace` project's root directory (the same directory as `Cargo.toml` and the `src` folder). Add the following content to `input.txt`:
    ```text
    Hello world!
    This is a test file.
    Hello again world.
    Replace the word world.
    World, world, world!
    ```
    We will use this file as input to our program.
7.  **Commit often:** As you implement the steps of this lab, commit your changes frequently using `git commit`. This saves your progress and makes it easy to revert if needed. Consider using [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `feat(lab03): setup quickreplace project and dependencies`, `feat(lab03): implemented argument parsing`) for a clear commit history.

## 6. Lab Steps (Iterative Development)

Now, let's build the program step by step by modifying the `src/main.rs` file. Open `src/main.rs` in your text editor.

*   **Step 1: Define the Program's Structure and Arguments**
    *   **Task:** Define the structure to hold the command-line arguments and add the necessary `use` statements.
    *   **Code Implementation:** Replace the *entire* content of `src/main.rs` with the following:
        ```rust
        // In src/main.rs

        use std::env; // For accessing command-line arguments
        use std::process; // For exiting the program
        use text_colorizer::*; // For coloring output

        #[derive(Debug)] // This allows us to print the struct using {:?}
        struct Arguments {
            target: String,      // The string pattern to find
            replacement: String, // The string to replace it with
            filename: String,    // The input file name
            output: String,      // The output file name
        }

        fn main() {
            // The main function will orchestrate parsing args,
            // reading file, replacing text, and writing file.
            // We'll fill this in later.
        }

        ```
    *   **Explanation:**
        *   `use std::env;`: This brings the `env` module from the standard library into scope, which we'll use to read command-line arguments.
        *   `use std::process;`: This brings the `process` module, which contains the `exit` function we'll use to terminate the program in case of errors.
        *   `use text_colorizer::*;`: This brings all public items from the `text_colorizer` crate into scope, allowing us to use its coloring methods like `.red()`.
        *   `#[derive(Debug)]`: This is an attribute macro provided by the standard library. It automatically generates code to make our `Arguments` struct printable using the debug format specifier `{:?}` with `println!`. This is useful for debugging.
        *   `struct Arguments { ... }`: We define a struct named `Arguments` to logically group the four required command-line parameters: `target`, `replacement`, `filename` (input), and `output`. Using a struct makes the code clearer than passing four separate strings around. Each field stores its value as a `String`.
        *   `fn main() { ... }`: The standard entry point for our executable. We leave its body empty for now as we build the pieces it will call.
    *   **Verification:** Save `src/main.rs`. Open your terminal in the `quickreplace` directory and run `cargo check`. It should compile successfully without errors, confirming your basic structure and dependencies are recognized.

*   **Step 2: Implement Argument Parsing**
    *   **Task:** Create a function to parse the command-line arguments provided to the program.
    *   **Code Implementation:** Add the following function *below* the `Arguments` struct definition and *above* the `main` function in `src/main.rs`:
        ```rust
        // In src/main.rs, add below Arguments struct:

        // Add this function:
        fn parse_args() -> Arguments {
            let args: Vec<String> = env::args().skip(1).collect(); // 1

            if args.len() != 4 { // 2
                // Call a usage function (will add next)
                eprintln!("{}{} wrong number of arguments: expected 4, got {}.", // 3
                    "Error:".red().bold(), " ", args.len()); // 3
                process::exit(1); // 4
            }

            Arguments { // 5
                target: args[0].clone(), // 6
                replacement: args[1].clone(),
                filename: args[2].clone(),
                output: args[3].clone()
            }
        }

        // Keep the main function below this
        // fn main() { ... }
        ```
    *   **Explanation:**
        *   `fn parse_args() -> Arguments`: This defines a function `parse_args` that takes no arguments (`()`) and is expected to return an `Arguments` struct (`-> Arguments`).
        *   `let args: Vec<String> = env::args().skip(1).collect();` (1):
            *   `env::args()` returns an *iterator* over the command-line arguments passed to the program. The first argument is always the path to the executable itself.
            *   `.skip(1)` skips the first element (the executable path), leaving only the arguments provided by the user.
            *   `.collect()` is a powerful method on iterators that gathers the elements into a collection. Here, we specify `Vec<String>` to collect the remaining arguments into a vector of `String`s.
        *   `if args.len() != 4 { ... }` (2): We check if the number of user-provided arguments is exactly 4 (target, replacement, input file, output file).
        *   `eprintln!("...")` (3): If the argument count is wrong, we print an error message. `eprintln!` prints to the standard error stream, which is typically where error messages should go, separating them from the program's normal output (which goes to standard output via `println!`). We use `text_colorizer` (`.red().bold()`) to make the "Error:" prefix stand out. Notice the space `" "` after the colored "Error:".
        *   `process::exit(1);` (4): If the argument count is wrong, we terminate the program immediately using `process::exit`. The argument `1` is a conventional non-zero exit code indicating that the program finished due to an error. An exit code of `0` typically indicates success.
        *   `Arguments { ... }` (5): If the argument count is correct, we construct and return an `Arguments` struct.
        *   `args[0].clone()` (6): We access elements of the `args` vector by index (`args[0]`, `args[1]`, etc.). Since the vector owns the `String`s, and we want the `Arguments` struct to own its own copies, we use `.clone()` for each argument. Cloning creates a new `String` with the same content. This is necessary because the `Arguments` struct will outlive the temporary `args` vector in this function.
    *   **Verification:** Save the file. Run `cargo check`. It should still compile without errors. Now, let's test the parsing logic. Run the program with different numbers of arguments:
        ```bash
        cargo run
        cargo run one two three
        cargo run one two three four five
        ```
        In each case except the last one, you should see the red error message printed to your terminal's error stream, and the program should exit. The last command will just finish quietly (or perhaps complain about the `main` function being empty, which is fine for now).

*   **Step 3: Add Usage Instructions**
    *   **Task:** Create a function to print helpful usage instructions when the program is run incorrectly.
    *   **Code Implementation:** Add the following function *above* `parse_args` in `src/main.rs`:
        ```rust
        // In src/main.rs, add below the use statements:

        // Add this function:
        fn print_usage() {
            eprintln!("{} - change occurrences of one string into another",
                env!("CARGO_PKG_NAME").green()); // 1
            eprintln!("Usage: {} <target> <replacement> <INPUT> <OUTPUT>",
                env!("CARGO_PKG_NAME").green()); // 1
        }

        // Keep the Arguments struct below this
        // Keep parse_args and main below this
        ```
    *   **Code Implementation (Modify `parse_args`):** Now, call `print_usage()` inside the `if args.len() != 4` block in your `parse_args` function:
        ```rust
        // In src/main.rs, inside the parse_args function:

        fn parse_args() -> Arguments {
            let args: Vec<String> = env::args().skip(1).collect();

            if args.len() != 4 {
                print_usage(); // <--- Add this line
                eprintln!("{}{} wrong number of arguments: expected 4, got {}.",
                    "Error:".red().bold(), " ", args.len());
                process::exit(1);
            }

            Arguments {
                target: args[0].clone(),
                replacement: args[1].clone(),
                filename: args[2].clone(),
                output: args[3].clone()
            }
        }
        // ... rest of the file
        ```
    *   **Explanation:**
        *   `fn print_usage() { ... }`: This function simply contains `eprintln!` calls to display how the program should be used.
        *   `env!("CARGO_PKG_NAME")` (1): This is a built-in macro provided by Cargo. At compile time, it inserts the value of the `name` field from your `Cargo.toml` file (which is `quickreplace`) as a string literal. This avoids hardcoding the program name and keeps it in sync with `Cargo.toml`. We use `.green()` from `text-colorizer` to make the program name stand out in the usage message.
        *   We call `print_usage()` before the error message in `parse_args` so the user sees the correct usage before being told they made an error.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. Now, test running the program with 0 or 3 arguments:
        ```bash
        cargo run
        cargo run one two three
        ```
        You should now see the green usage message followed by the red error message, printed to standard error.

*   **Step 4: Implement File Reading**
    *   **Task:** Read the content of the input file specified in the arguments.
    *   **Code Implementation:** Add the following `use` statement at the top and modify the `main` function:
        ```rust
        // In src/main.rs, add at the top:
        use std::fs; // For file system operations

        // ... other use statements and functions ...

        // In src/main.rs, modify the main function:
        fn main() {
            let args = parse_args(); // 1

            let data = match fs::read_to_string(&args.filename) { // 2
                Ok(v) => v, // 3
                Err(e) => { // 4
                    eprintln!("{}{} failed to read from file '{}': {:?}",
                        "Error:".red().bold(), " ", args.filename, e);
                    process::exit(1);
                }
            };

            // We will add the replacement and writing logic here later

            // Temporarily print the data to verify reading
            // println!("Successfully read data:\n{}", data); // Optional: uncomment for verification
        }

        // ... rest of the file
        ```
    *   **Explanation:**
        *   `use std::fs;`: This line imports the `fs` module from the standard library, which provides functions for interacting with the file system, including reading and writing files.
        *   `let args = parse_args();` (1): We first call our `parse_args` function to get the `Arguments` struct containing the file names.
        *   `fs::read_to_string(&args.filename)` (2): This is the core of the file reading. `fs::read_to_string` is a function that attempts to open the file specified by the path (passed as a reference `&str` converted from `&args.filename`) and read its entire content into a `String`. This function returns a `Result<String, std::io::Error>`. `Result` is an enum that is either `Ok(value)` on success or `Err(error)` on failure.
        *   `match ... { Ok(v) => v, ... }` (3): We use a `match` statement to handle the `Result`. If the `Result` is `Ok(v)`, it means the file was read successfully, and the content is in the `String` `v`. We return this `String` from the `match` expression and bind it to the `data` variable.
        *   `match ... { Err(e) => { ... }, ... }` (4): If the `Result` is `Err(e)`, it means an error occurred (e.g., file not found, permission denied). The error details are in `e`. We print an informative error message to standard error using `eprintln!`, including the file name and the error details (`e`), and then exit the program with a non-zero status code.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. Now, run the program with your `input.txt` file:
        ```bash
        cargo run find replace input.txt output.txt
        ```
        The program should run without error messages (as reading `input.txt` should succeed). If you uncommented the `println!("Successfully read data:\n{}", data);` line, you would see the content of `input.txt` printed. Now, test with a non-existent file:
        ```bash
        cargo run find replace non_existent_file.txt output.txt
        ```
        You should see a red error message indicating that reading the file failed, including details about the specific error (like "No such file or directory").

*   **Step 5: Implement the Replacement Logic with Regex**
    *   **Task:** Create a function that performs the find-and-replace using the `regex` crate.
    *   **Code Implementation:** Add the following `use` statement at the top and the `replace` function *below* `parse_args`:
        ```rust
        // In src/main.rs, add at the top:
        use regex::Regex; // For using regular expressions

        // ... other use statements and functions ...

        // In src/main.rs, add below the parse_args function:

        fn replace(target: &str, replacement: &str, text: &str) // 1
        -> Result<String, regex::Error> // 2
        {
            let regex = Regex::new(target)?; // 3
            Ok(regex.replace_all(text, replacement).to_string()) // 4
        }

        // Keep the main function below this
        // fn main() { ... }
        ```
    *   **Code Implementation (Modify `main`):** Now, call this `replace` function from `main`:
        ```rust
        // In src/main.rs, inside the main function:

        fn main() {
            let args = parse_args();

            let data = match fs::read_to_string(&args.filename) {
                Ok(v) => v,
                Err(e) => {
                    eprintln!("{}{} failed to read from file '{}': {:?}",
                        "Error:".red().bold(), " ", args.filename, e);
                    process::exit(1);
                }
            };

            let replaced_data = match replace(&args.target, &args.replacement, &data) { // 5
                Ok(v) => v, // 6
                Err(e) => { // 7
                    eprintln!("{}{} failed to replace text: {:?}",
                        "Error:".red().bold(), " ", e);
                    process::exit(1);
                }
            };

            // We will add the writing logic here later

            // Temporarily print the replaced data to verify replacement
            // println!("Successfully replaced data:\n{}", replaced_data); // Optional: uncomment for verification
        }

        // ... rest of the file
        ```
    *   **Explanation:**
        *   `use regex::Regex;`: Imports the main `Regex` type from the `regex` crate.
        *   `fn replace(...)` (1): Defines the `replace` function. It takes references to strings (`&str`) for the `target` pattern, the `replacement` text, and the `text` to search within. Using `&str` is efficient as it avoids copying large strings.
        *   `-> Result<String, regex::Error>` (2): The function returns a `Result`. On success (`Ok`), it will return a new `String` containing the text after replacements. On failure (`Err`), it will return a `regex::Error`, which can happen if the `target` pattern is an invalid regular expression.
        *   `let regex = Regex::new(target)?;` (3):
            *   `Regex::new(target)` attempts to compile the `target` string into a valid regular expression object. This can fail if the pattern is malformed.
            *   The `?` operator is a concise way to handle `Result` (and `Option`). If `Regex::new(target)` returns `Ok(regex_object)`, the `regex_object` is unwrapped, and the `let regex = ...` assignment proceeds. If `Regex::new(target)` returns `Err(error)`, the `error` is immediately returned from the *current function* (`replace`), wrapped back into an `Err(error)`. This replaces the boilerplate `match` we used in `main`.
        *   `regex.replace_all(text, replacement).to_string()` (4):
            *   `regex.replace_all(text, replacement)` performs the actual replacement. It searches the `text` using the compiled `regex` pattern and replaces all matches with the `replacement` string. This method returns a `Cow<'_, str>`, which is an optimized type that might borrow from the original `text` or own a new `String` depending on the replacement.
            *   `.to_string()` converts the `Cow` into a standard `String`, which is what our function is declared to return on success.
            *   `Ok(...)`: We wrap the resulting `String` in `Ok()` because this is the success path of the `Result` our function returns.
        *   `match replace(...)` (5): Back in `main`, we call our new `replace` function. Since `replace` returns a `Result`, we use a `match` statement again to handle its outcome.
        *   `Ok(v) => v` (6): If `replace` succeeds, the new string with replacements is in `v`, and we bind it to `replaced_data`.
        *   `Err(e) => { ... }` (7): If `replace` returns an error (because the user-provided `target` was an invalid regex pattern), we print a red error message to standard error, including the specific `regex::Error` details, and exit.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. Now, run the program with valid arguments:
        ```bash
        cargo run world planet input.txt output.txt
        ```
        This should replace all occurrences of "world" with "planet". If you uncommented the temporary `println!`, you would see the modified text with "planet" substituted. Now, test with an invalid regex pattern:
        ```bash
        cargo run "([)" replace input.txt output.txt
        ```
        The pattern `"([)"` is invalid because the opening parenthesis is not matched. You should see a red error message related to the regex compilation failure.

*   **Step 6: Implement File Writing**
    *   **Task:** Write the modified text data to the specified output file.
    *   **Code Implementation:** Add the following `use` statement (if you didn't already in step 4) and modify the `main` function further:
        ```rust
        // In src/main.rs, ensure you have:
        use std::fs; // For file system operations

        // ... other use statements and functions ...

        // In src/main.rs, modify the main function:
        fn main() {
            let args = parse_args();

            let data = match fs::read_to_string(&args.filename) {
                Ok(v) => v,
                Err(e) => {
                    eprintln!("{}{} failed to read from file '{}': {:?}",
                        "Error:".red().bold(), " ", args.filename, e);
                    process::exit(1);
                }
            };

            let replaced_data = match replace(&args.target, &args.replacement, &data) {
                Ok(v) => v,
                Err(e) => {
                    eprintln!("{}{} failed to replace text: {:?}",
                        "Error:".red().bold(), " ", e);
                    process::exit(1);
                }
            };

            match fs::write(&args.output, &replaced_data) { // 1
                Ok(_) => {}, // 2
                Err(e) => { // 3
                    eprintln!("{}{} failed to write to file '{}': {:?}",
                        "Error:".red().bold(), " ", args.output, e);
                    process::exit(1);
                }
            };

            // Optional: Indicate success
            // println!("Replacement successful: '{}' -> '{}' in '{}', output to '{}'",
            //          args.target, args.replacement, args.filename, args.output);
        }

        // ... rest of the file
        ```
    *   **Explanation:**
        *   `fs::write(&args.output, &replaced_data)` (1): This function attempts to write the content of the `replaced_data` string to the file path specified by `args.output`. It takes references (`&str`) to the path and the data. If the file doesn't exist, it will be created. If it does exist, its contents will be overwritten. This function returns a `Result<(), std::io::Error>`. The `()` type (pronounced "unit") signifies that on success, there is no meaningful value returned.
        *   `Ok(_) => {}` (2): We match on the `Result`. If it's `Ok`, the write operation was successful. The `_` is a wildcard pattern that ignores the success value (the `()`). The empty `{}` block means we do nothing on success – the program simply continues.
        *   `Err(e) => { ... }` (3): If `fs::write` returns an `Err(e)`, an error occurred during writing (e.g., disk full, permission denied, invalid path). We print a red error message to standard error, including the output file name and the error details, and exit the program with status 1.
    *   **Verification:** Save the file. Run `cargo check`. It should compile. Now, run the program with your sample `input.txt`:
        ```bash
        cargo run world planet input.txt output.txt
        ```
        This command should execute successfully without printing any error messages to standard error. Check the `quickreplace` project directory. A new file named `output.txt` should have been created or updated. Open `output.txt` in a text editor. Its content should be the same as `input.txt` but with all instances of "world" replaced by "planet".

Congratulations, you have built the complete `quickreplace` utility!

## 7. Running the Application / Testing

To run the completed application, navigate to the `quickreplace` directory in your terminal and use the `cargo run` command followed by the four arguments: `<target>`, `<replacement>`, `<INPUT_FILE>`, `<OUTPUT_FILE>`.

**Example:**

To replace "test" with "demo" in `input.txt` and save the result to `demo.txt`:

```bash
cargo run test demo input.txt demo.txt
```

**Expected Behavior:**

*   If arguments are missing or incorrect count: Prints usage and a red error message to standard error, then exits.
*   If input file doesn't exist or cannot be read: Prints a red error message about the read failure to standard error, then exits.
*   If target pattern is invalid regex: Prints a red error message about the regex failure to standard error, then exits.
*   If output file cannot be written (e.g., invalid path, permissions): Prints a red error message about the write failure to standard error, then exits.
*   If successful: The program finishes silently (or prints your optional success message if uncommented), and a new file named `<OUTPUT_FILE>` is created/overwritten with the content of `<INPUT_FILE>` after all occurrences of `<target>` have been replaced by `<replacement>`.

**Testing with Different Inputs:**

*   Try replacing common words like "This" with "That".
*   Try replacing with an empty string (`cargo run test "" input.txt output.txt`) to delete occurrences.
*   Try replacing special characters (requires understanding regex escaping). E.g., to replace `.` with `-`: `cargo run "\." "-" input.txt output.txt` (The `\` needs to be escaped as `\\` in the shell if not in quotes, but quotes handle it here).
*   Test error cases: Run with 0, 1, 2, 3, or 5 arguments. Run with a non-existent input file. Run with an invalid regex pattern like `[`.

## 8. Key Concepts Review

*   **Command-Line Arguments (`std::env`)**: Accessing arguments passed to the program using `env::args()`, skipping the executable path, and collecting them into a `Vec<String>`.
*   **Standard Error (`eprintln!`)**: Using `eprintln!` to print error or usage messages to the standard error stream, separate from normal output.
*   **Program Exit Status (`std::process::exit`)**: Terminating the program with a specific exit code (0 for success, non-zero for failure).
*   **Structs**: Defining custom data types (`Arguments`) to group related data fields.
*   **Ownership and Borrowing**: Passing string data by reference (`&str`) to functions like `replace` and `fs::read_to_string`/`fs::write` to avoid unnecessary copying. Using `.clone()` when ownership transfer is needed (`parse_args`).
*   **File I/O (`std::fs`)**: Reading file contents into a string (`fs::read_to_string`) and writing string contents to a file (`fs::write`).
*   **Error Handling (`Result`, `match`, `?`)**: Using the `Result<T, E>` enum to represent operations that can succeed (`Ok(T)`) or fail (`Err(E)`). Using `match` statements to explicitly handle both success and error cases. Using the `?` operator as a concise way to propagate errors up the call stack within functions that return `Result`.
*   **External Crates & `Cargo.toml`**: Adding dependencies to the `[dependencies]` section of `Cargo.toml` and using the `use` keyword to bring items from those crates into scope.
*   **Regular Expressions (`regex`)**: Using the `regex` crate to compile a pattern (`Regex::new`) and perform find-and-replace operations (`regex.replace_all`). Understanding that regex compilation can fail.
*   **Text Coloring (`text-colorizer`)**: Using methods like `.green()`, `.red()`, `.bold()` provided by the `text-colorizer` crate to format terminal output.
*   **`env!("CARGO_PKG_NAME")` macro**: A compile-time macro provided by Cargo to embed the package name from `Cargo.toml`.

## 9. (Optional) Challenges / Next Steps

1.  **More Robust Argument Parsing:** Instead of manually indexing `args[0]`, `args[1]`, etc., explore the `clap` crate ([https://crates.io/crates/clap](https://crates.io/crates/clap)) which provides a powerful and user-friendly way to define, parse, and validate command-line arguments with help text generation. Refactor `parse_args` to use `clap`.
2.  **Custom Error Type:** The current error handling uses `match` and prints errors directly. For larger applications, it's better to define a custom error `enum` using crates like `thiserror` ([https://crates.io/crates/thiserror](https://crates.io/crates/thiserror)) or `anyhow` ([https://crates.io/crates/anyhow](https://crates.io/crates/anyhow)). Refactor the `main`, `read`, `replace`, and `write` logic to return a single, unified custom error type from `main`.
3.  **Handle Binary Data:** The current implementation reads the entire file into a `String`, which assumes the input is valid UTF-8 text. Modify the program to work with raw bytes (`Vec<u8>`) instead of `String` using `fs::read` and `fs::write`. Note that the `regex` crate has limited support for non-UTF-8 data, so you might need a different approach or library for true binary find-and-replace.
4.  **Case-Insensitive Replacement:** Modify the `replace` function to compile the regex with a case-insensitive flag. Consult the `regex` crate documentation for `RegexBuilder` or flags.
5.  **Add Options/Flags:** Use `clap` (from Challenge 1) to add optional command-line flags, such as `--case-insensitive` or `--global` (although `replace_all` is already global).

## 10. (Optional) Troubleshooting

*   **`error: could not find `Cargo.toml`...`**: Ensure you are running `cargo` commands from the `quickreplace` project directory (`cd quickreplace`).
*   **Compile Errors related to `text_colorizer` or `regex`**:
    *   Verify that you added `text-colorizer = "1"` and `regex = "1"` correctly under `[dependencies]` in `Cargo.toml`.
    *   Run `cargo build` or `cargo run` once to make Cargo download and build the dependencies.
    *   Check your `use` statements (`use text_colorizer::*;`, `use regex::Regex;`) are correct.
*   **`error[E0425]: cannot find function `read_to_string` in module `fs`** : Ensure you have `use std::fs;` at the top of your `src/main.rs`.
*   **Argument Parsing Errors**: If your program crashes or behaves unexpectedly with different argument counts, review the logic in `parse_args`, especially the `skip(1)` and `args.len() != 4` check. Use `dbg!(&args);` inside `parse_args` before the `if` statement to inspect the contents of the `args` vector.
*   **File Not Written/Incorrect Output**:
    *   Verify the output file name provided on the command line is valid for your operating system.
    *   Ensure the program ran successfully without printing error messages to standard error.
    *   Check permissions for the directory where the output file should be created.
    *   Temporarily uncomment the `println!("Successfully replaced data:\n{}", replaced_data);` line in `main` to verify that the replacement logic produced the expected string *before* writing it to the file.

## 11. Conclusion

You have successfully built a practical command-line utility in Rust that performs file find-and-replace! This lab reinforced fundamental Rust concepts like functions, structs, and error handling with `Result` and `match`. More importantly, you gained experience working with essential parts of the standard library (`std::env` for arguments, `std::fs` for file I/O) and integrated powerful external crates (`regex` for text processing, `text-colorizer` for better UI). This is a solid foundation for building more complex and useful command-line tools in Rust.

Remember to commit the final state of your project to your Git repository using `git commit` and push your changes (`git push`). Adopting Conventional Commits will make your project history cleaner and easier to navigate.

## 12. Final Solution Code

Here is the complete code for `src/main.rs` after completing all the steps in this lab:

```rust
// Final Code for src/main.rs

use std::env; // For accessing command-line arguments
use std::process; // For exiting the program
use text_colorizer::*; // For coloring output
use std::fs; // For file system operations
use regex::Regex; // For using regular expressions


#[derive(Debug)] // This allows us to print the struct using {:?}
struct Arguments {
    target: String,      // The string pattern to find
    replacement: String, // The string to replace it with
    filename: String,    // The input file name
    output: String,      // The output file name
}

fn print_usage() {
    eprintln!("{} - change occurrences of one string into another",
        env!("CARGO_PKG_NAME").green());
    eprintln!("Usage: {} <target> <replacement> <INPUT> <OUTPUT>",
        env!("CARGO_PKG_NAME").green());
}

fn parse_args() -> Arguments {
    let args: Vec<String> = env::args().skip(1).collect();

    if args.len() != 4 {
        print_usage();
        eprintln!("{}{} wrong number of arguments: expected 4, got {}.",
            "Error:".red().bold(), " ", args.len());
        process::exit(1);
    }

    Arguments {
        target: args[0].clone(),
        replacement: args[1].clone(),
        filename: args[2].clone(),
        output: args[3].clone()
    }
}

fn replace(target: &str, replacement: &str, text: &str)
-> Result<String, regex::Error>
{
    let regex = Regex::new(target)?;
    Ok(regex.replace_all(text, replacement).to_string())
}

fn main() {
    let args = parse_args();

    let data = match fs::read_to_string(&args.filename) {
        Ok(v) => v,
        Err(e) => {
            eprintln!("{}{} failed to read from file '{}': {:?}",
                "Error:".red().bold(), " ", args.filename, e);
            process::exit(1);
        }
    };

    let replaced_data = match replace(&args.target, &args.replacement, &data) {
        Ok(v) => v,
        Err(e) => {
            eprintln!("{}{} failed to replace text: {:?}",
                "Error:".red().bold(), " ", e);
            process::exit(1);
        }
    };

    match fs::write(&args.output, &replaced_data) {
        Ok(_) => {}, // The write function returns () on success, which we ignore with _
        Err(e) => {
            eprintln!("{}{} failed to write to file '{}': {:?}",
                "Error:".red().bold(), " ", args.output, e);
            process::exit(1);
        }
    };
    // Optional: Indicate success if desired, e.g.:
    // println!("Replacement successful: '{}' -> '{}' in '{}', output to '{}'",
    //          args.target, args.replacement, args.filename, args.output);
}
```

Final code for `Cargo.toml`:

```toml
# Final Code for quickreplace/Cargo.toml

[package]
name = "quickreplace"
version = "0.1.0"
edition = "2024" # Changed from 2021 as per instructions

# See more keys and their definitions at
# https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
text-colorizer = "1"
regex = "1"
```

And the sample input file `input.txt`:

```text
# Sample input.txt

Hello world!
This is a test file.
Hello again world.
Replace the word world.
World, world, world!
```

## 13. Check Questions (To Test Understanding)

1.  In `parse_args`, why do we use `env::args().skip(1).collect()` instead of just `env::args().collect()`?
2.  Explain the difference between `println!` and `eprintln!`, and when you would use each in a command-line application like `quickreplace`.
3.  The `fs::read_to_string` function returns a `Result`. Why is handling the `Err` variant of this `Result` crucial in our `main` function?
4.  In the `replace` function, what is the purpose of the `?` operator after `Regex::new(target)`? What type must `replace` return for `?` to work on a `Result<T, E>`?
5.  Why did we use `.clone()` when creating the `Arguments` struct fields from the `args` vector in `parse_args`?

## 14. Detailed Answers to Check Questions

1.  `env::args()` returns an iterator where the *first* element (`args[0]`) is the path to the executable program itself (e.g., `"target/debug/quickreplace"`). The user's actual arguments start from the second element. We use `.skip(1)` to discard the executable path and only process the arguments provided by the user (the target, replacement, input file, and output file).
2.  `println!` writes to the **standard output** stream, while `eprintln!` writes to the **standard error** stream. In a command-line application, standard output is typically used for the program's normal output (like the result of processing), whereas standard error is used for diagnostic messages, warnings, and errors. This separation allows users to redirect the program's successful output to a file or pipe it to another command (`program > output.txt`), while still seeing error messages printed directly to the console. In `quickreplace`, we use `eprintln!` for usage instructions and error messages because they are not part of the program's successful result.
3.  `fs::read_to_string` can fail for various reasons (e.g., the file doesn't exist, lack of read permissions, the file is not valid UTF-8). Handling the `Err` variant using `match` (or `?` if `main` returned `Result`) is crucial because ignoring it would lead to a `panic!` if the file operation failed. A `panic!` would crash the entire program unexpectedly. By matching on `Err`, we can catch the specific error, print an informative message to the user using `eprintln!`, and exit gracefully with a non-zero status code (`process::exit(1)`), which signals to the operating system or calling scripts that the program failed.
4.  The `?` operator is syntactic sugar for handling `Result` and `Option` enums. When applied to a `Result<T, E>`, it checks if the result is `Ok(T)` or `Err(E)`. If it's `Ok(T)`, the `Ok` value (`T`) is extracted, and the operation continues. If it's `Err(E)`, the error value (`E`) is immediately returned from the *current function* (`replace` in this case), wrapped in an `Err`. For `?` to work on `Result<T, E>`, the function it's used within *must* return a `Result` type where the error type is compatible with or converts into the function's declared error type (`regex::Error` for `replace`).
5.  We used `.clone()` because the `args` vector owns the `String`s collected from `env::args()`. The `parse_args` function creates this vector, and the vector is dropped (its memory is freed) when the function finishes. However, the `Arguments` struct that we return from `parse_args` needs to hold onto those string values *after* the function returns. By calling `.clone()`, we create *new* `String`s with the same content, transferring ownership of these new strings to the `Arguments` struct. This ensures the struct's fields remain valid after the `args` vector is gone.

***
This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025. All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech