**Lab 07: `String` and `&str` Fundamentals**

Strings are a fundamental data type in most programming languages, and Rust's approach with `String` (owned) and `&str` (borrowed slice) is powerful but requires understanding. This hands-on lab provides practical exercises to help solidify your understanding of these core types, how they relate, and how to use them effectively with common collections like vectors and tuples.

By the end of this lab, you will be able to:

*   Distinguish between owned `String` and borrowed `&str`.
*   Convert between `String` and `&str`.
*   Work with vectors of `String` and `&str`.
*   Include strings and string slices in tuples.
*   Perform basic string manipulation and checks.

This lab involves writing and running simple Rust code snippets in your `main.rs` file. While the exercises are focused, they build foundational skills applicable to any Rust project involving text data. Rust's strict rules around ownership, borrowing, and UTF-8 encoding provide robustness, and practicing with `String` and `&str` is key to writing idiomatic Rust code.

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, loops, `println!`).
    *   Understanding of `mut` for mutability.
    *   Familiarity with `Vec` (vectors) and tuples.
    *   A conceptual introduction to Rust's ownership and borrowing system is helpful, but these exercises will help illustrate it.
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. If you are working with a Git repository, check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial string lab setup`, `feat: complete exercise 1`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new string_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd string_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `string_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file. Use comments (`//`) to separate and label the code for each exercise.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`.

1.  **Basic String Types**
    Declare a `String` variable holding "Hello" and an `&str` literal holding "World". Print both to the console.
    *   Declare an owned string using `String::from()` or `.to_string()`.
    *   String literals like `"World"` are automatically `&str`.
    *   Use `println!` to display them.
    *   **Verification:** Run `cargo run`. You should see "Hello" and "World" printed.

2.  **String from String Slice**
    Create a `&str` literal. Convert this literal into an owned `String`. Print the `String`.
    *   You can convert `&str` to `String` using `.to_string()` or `String::from()`.
    *   **Verification:** Run `cargo run`. You should see the converted string printed.

3.  **String Slice from String**
    Create an owned `String`. Create an `&str` slice that references a portion (e.g., the first half) of the characters in the original `String`. Print both the original `String` and the slice.
    *   Create an owned string using `String::from()`.
    *   Use indexing `[start..end]` on the string to create a slice.
    *   **Crucial Line Example:**
        ```rust
        let my_slice = &my_string[0..7]; // Example slice
        ```
    *   **Verification:** Run `cargo run`. You should see the full string and the sliced portion printed.

4.  **Vector of Owned Strings**
    Create a `Vec<String>`. Add three different owned `String` values to the vector. Iterate over the vector using a shared reference (`&Vec<String>`) and print each `String`.
    *   Create a mutable vector `Vec<String>`.
    *   Use `.push()` to add `String` values.
    *   Iterate using a `for` loop over a shared reference to the vector.
    *   **Crucial Line Example:**
        ```rust
        for s in &string_vec { // Iterate by reference
             // ... print s ...
        }
        ```
    *   **Verification:** Run `cargo run`. You should see the three strings printed, one per line.

5.  **Vector of String Slices**
    Create several `&str` literals. Store these literals in a `Vec<&str>`. Iterate over the vector and print each `&str` slice.
    *   Declare a vector with the type `Vec<&str>`.
    *   Use the `vec![]` macro to initialize it with string literals.
    *   Iterate using a `for` loop.
    *   **Crucial Line Example:**
        ```rust
        let slice_vec: Vec<&str> = vec![/* ... literals ... */];
        ```
    *   **Verification:** Run `cargo run`. You should see the slice values printed.

6.  **Appending Strings**
    Start with an owned `String`. Append the content of an `&str` literal to it. Print the modified `String`.
    *   Declare a *mutable* owned string.
    *   Use the `.push_str()` method or the `+=` operator.
    *   **Crucial Line Example:**
        ```rust
        my_string.push_str("...");
        ```
    *   **Verification:** Run `cargo run`. You should see the initial string followed by the appended text.

7.  **Tuples with Strings**
    Create a tuple that contains one `String` and one `&str`. Access and print both elements of the tuple.
    *   Create a tuple using parentheses `()`. Include an owned string and a string literal.
    *   Access elements using dot notation followed by the index (`.0`, `.1`).
    *   **Crucial Line Example:**
        ```rust
        let my_tuple = (String::from("..."), "...");
        ```
    *   **Verification:** Run `cargo run`. You should see both parts of the tuple printed.

8.  **Checking for Substring**
    Create a `&str` literal or an owned `String`. Check if it contains a specific substring. Print whether the substring was found.
    *   Use the `.contains()` method available on both `String` and `&str`.
    *   `.contains()` takes an `&str` as input and returns a boolean.
    *   **Crucial Line Example:**
        ```rust
        let found = text.contains("...");
        ```
    *   **Verification:** Run `cargo run`. Print `true` or `false` based on the checks.

9.  **Building a String**
    Create several `&str` literals representing parts of a sentence. Combine these `&str` parts to form a single new owned `String`. Print the resulting `String`.
    *   Consider using the `format!` macro or creating a new `String` and using `.push_str()`.
    *   **Crucial Line Example (using format!):**
        ```rust
        let full_sentence = format!("{}{}{}", part1, part2, part3);
        ```
    *   **Verification:** Run `cargo run`. You should see the combined sentence printed as a single string.

10. **Function with String Slice Input**
    Write a function that accepts a single argument of type `&str`. The function should return a new owned `String` based on the input slice (e.g., convert it and append some text). In `main`, create an `&str` literal, call the function, and print the returned `String`.
    *   Define a function signature `fn process_slice(input: &str) -> String`.
    *   Inside the function, convert the input `&str` to a `String` and build the result.
    *   Call the function from `main` and print the returned value.
    *   **Crucial Line Example (function signature):**
        ```rust
        fn process_slice(input: &str) -> String {
            // ... function body ...
        }
        ```
    *   **Verification:** Run `cargo run`. You should see the original slice printed (from main) and then the string returned by the function printed.

**Running the Application**

Once you have implemented all 10 exercises within your `main` function, you can run the complete program from your terminal in the project directory:

```bash
cargo run
```

Observe the output. You should see the results of each exercise printed to the console.

**Key Concepts Review**

*   `String`: Owned, mutable string data on the heap.
*   `&str`: Borrowed, immutable view into string data. Often comes from literals or `String` slices.
*   Conversion between `String` and `&str` (`.to_string()`, `String::from()`, slicing `&string[...]`).
*   Using `Vec` with both `String` and `&str`.
*   String concatenation (`.push_str()`, `+=`, `format!`).
*   Basic string methods (`.contains()`).
*   Tuples can hold different string types.
*   Passing string data to functions typically uses `&str` to avoid unnecessary copying or moving.

**Challenges / Next Steps**

1.  Explore iterating over a string using `.chars()` (yields characters) and `.bytes()` (yields bytes). How do they differ for multi-byte characters?
2.  Implement Exercise 6 using the `format!` macro instead of `push_str` or `+=`. When would `format!` be a better choice?

**Troubleshooting**

*   **"use of moved value" error:** This indicates you attempted to use an owned `String` after it was moved. Remember `String` is not `Copy`. Pass `&String` or `&mut String` to borrow, or use `.clone()` for an independent copy.
*   **"cannot borrow `...` as mutable" error:** You are trying to modify a string or vector through a shared reference (`&`). Ensure you have a mutable variable (`let mut ...`) and are using a mutable reference (`&mut ...`) when modification is required.

**Conclusion**

You've completed practical exercises working with Rust's core string types, `String` and `&str`. Understanding their distinction and how to use them with vectors and functions is crucial for writing effective Rust code.

**Final Solution Code (src/main.rs)**

Here is the complete `main` function containing the solutions for all 10 exercises. Review it to compare with your own implementations.

```rust
// src/main.rs

fn main() {
    println!("--- Rust String Lab Solutions ---");

    // 1. Exercise: Basic String Types
    println!("\n--- Exercise 1: Basic String Types ---");
    let my_string = String::from("Hello"); // Owned String
    let my_slice = "World"; // &str literal
    println!("Owned String: {}", my_string);
    println!("String Slice: {}", my_slice);


    // 2. Exercise: String from String Slice
    println!("\n--- Exercise 2: String from String Slice ---");
    let literal_slice: &str = "Convert this slice";
    let owned_from_slice: String = literal_slice.to_string(); // Convert &str to String
    // Alternative: let owned_from_slice = String::from(literal_slice);
    println!("Original slice: {}", literal_slice);
    println!("Owned string from slice: {}", owned_from_slice);


    // 3. Exercise: String Slice from String
    println!("\n--- Exercise 3: String Slice from String ---");
    let base_string = String::from("ThisIsASliceableString");
    // Create a slice referencing the first 7 bytes (characters in this case)
    let slice_from_string: &str = &base_string[0..7];
    println!("Original string: {}", base_string);
    println!("Slice from string: {}", slice_from_string);


    // 4. Exercise: Vector of Owned Strings
    println!("\n--- Exercise 4: Vector of Owned Strings ---");
    let mut owned_strings_vec: Vec<String> = Vec::new();
    owned_strings_vec.push(String::from("Apple"));
    owned_strings_vec.push("Banana".to_string()); // Another way to create String
    owned_strings_vec.push(String::from("Cherry"));
    println!("Iterating over Vec<String> (by reference):");
    for s in &owned_strings_vec { // Iterates over &String references
        println!("Element: {}", s);
    }
    // After this loop, owned_strings_vec is still usable.


    // 5. Exercise: Vector of String Slices
    println!("\n--- Exercise 5: Vector of String Slices ---");
    // Store string literals directly in a vector of &str
    let slice_vec: Vec<&str> = vec!["slice_a", "slice_b", "slice_c"];
    println!("Iterating over Vec<&str>:");
    for s in &slice_vec { // Iterates over & &str (auto-dereferences to &str)
         println!("Element: {}", s);
    }


    // 6. Exercise: Appending Strings
    println!("\n--- Exercise 6: Appending Strings ---");
    let mut base_string_for_append = String::from("Initial");
    let slice_to_append = " appended";
    println!("Before append: {}", base_string_for_append);
    base_string_for_append.push_str(slice_to_append); // Append &str using push_str
    println!("After push_str: {}", base_string_for_append);
    // Example using += (requires mutable String on left, &str on right)
    base_string_for_append += " more.";
     println!("After +=: {}", base_string_for_append);


    // 7. Exercise: Tuples with Strings
    println!("\n--- Exercise 7: Tuples with Strings ---");
    let string_tuple = (String::from("TupleOwned"), "TupleBorrowed");
    println!("Tuple element 0 (String): {}", string_tuple.0);
    println!("Tuple element 1 (&str): {}", string_tuple.1);


    // 8. Exercise: Checking for Substring
    println!("\n--- Exercise 8: Checking for Substring ---");
    let text_to_check = "This text contains the word Rust.";
    let has_rust = text_to_check.contains("Rust");
    let has_python = text_to_check.contains("Python");
    println!("Does '{}' contain 'Rust'? {}", text_to_check, has_rust);
    println!("Does '{}' contain 'Python'? {}", text_to_check, has_python);


    // 9. Exercise: Building a String
    println!("\n--- Exercise 9: Building a String ---");
    let part_a = "Building ";
    let part_b = "a ";
    let part_c = "string.";
    let built_string = format!("{}{}{}", part_a, part_b, part_c); // Use format! macro
    println!("Built string using format!: {}", built_string);
    // Alternative using String::new() and push_str
    let mut built_string_alt = String::new();
    built_string_alt.push_str(part_a);
    built_string_alt.push_str(part_b);
    built_string_alt.push_str(part_c);
    println!("Built string using push_str: {}", built_string_alt);


    // 10. Exercise: Function with String Slice Input
    println!("\n--- Exercise 10: Function with String Slice Input ---");
     // Define the function (can be before or after main, typically before)
    fn process_slice(input: &str) -> String {
        let mut result = input.to_string(); // Convert input &str to String
        result.push_str(" (processed by function)"); // Append to the new String
        result // Return the owned String
    }
    let input_slice_for_fn = "Some data";
    let processed_result = process_slice(input_slice_for_fn);
    println!("Original slice used in function: {}", input_slice_for_fn);
    println!("Result from function: {}", processed_result);


    println!("\n--- Lab Complete ---");
}

// The function definition for Exercise 10, placed outside main
/*
fn process_slice(input: &str) -> String {
    let mut result = input.to_string();
    result.push_str(" (processed by function)");
    result
}
*/


```

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete string lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).


This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

