# Lab 05: Array Fundamentals

Primary Learning Objective: To understand the core features and behaviors of fixed-size arrays (`[T; N]`) in Rust.

By the end of this lab, you will be able to:

*   Create arrays with explicit type/size annotations and using repetition syntax.
*   Access and modify array elements using indexing.
*   Iterate over array elements using immutable (`.iter()`) and mutable (`.iter_mut()`) iterators.
*   Create and use slices (`&[]`) to view parts of an array.
*   Understand how array behavior with respect to `Copy` and `Move` semantics differs based on the element type.
*   Compare arrays for equality.

Scenario

Imagine you're working on a system where you need to process data with fixed, known dimensions – for example, representing coordinates in 3D space, storing a small, unchanging set of configuration values, or handling data blocks of a specific size. Arrays are ideal for these situations because their fixed size allows for potential stack allocation, leading to efficient access and predictable memory usage compared to the heap-allocated, dynamically sized `Vec<T>`. By practicing with arrays, you'll learn when and how to leverage these properties effectively.

Prerequisites

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. This lab is based on Rust `edition = "2024"`. We assume the following tool versions: `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`. Ensure you have at least Rust 1.56.0 or later.
    *   A text editor or IDE: Such as VS Code with `rust-analyzer`, Sublime Text, Vim, Emacs, etc.
    *   Access to a Terminal or Command Prompt.
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`).
    *   Understanding of primitive types (integers, floats, booleans, characters).
    *   Familiarity with `Cargo.toml` for project configuration.
    *   Basic command-line usage.
    *   An initial understanding of ownership and borrowing concepts (this lab will help solidify them in the context of arrays).
*   **Setup Verification:** Open your terminal and confirm your Rust installation by running:
    ```bash
    rustc --version
    cargo --version
    ```
    Verify the versions displayed. If Rust is not found or the versions are significantly older, consider updating via `rustup update` or following the installation guide: [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install).

Setup Instructions

1.  **Navigate to your repository and branch:** Before creating the project, ensure you are in your training repository's root directory and on the correct Git branch for this lab. Check for any uncommitted changes from previous work.
2.  **Create a New Rust Project:**
    Open your terminal, navigate to the directory within your repository where you organize your lab projects, and run:
    ```bash
    cargo new rust_arrays_lab --edition 2024
    ```
    This command creates a new `rust_arrays_lab` directory with the necessary basic files (`Cargo.toml`, `src/main.rs`). We are using the `2024` edition of Rust, which includes various improvements. Rust editions (2015, 2018, 2021, 2024) are a mechanism for evolving the language in a backwards-compatible way.
3.  **Navigate into the project directory:**
    ```bash
    cd rust_arrays_lab
    ```
4.  No external dependencies are required for this lab.
5.  **Commit often:** As you implement the steps of this lab, commit your changes frequently using `git commit`. This saves your progress and creates useful checkpoints. Consider using [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `feat(arrays): initial project setup`, `feat(arrays): completed fixed size creation exercise`) for a clear commit history.

Lab Steps (Iterative Development)

Open `src/main.rs` in your text editor. You will write all the code for the exercises within the `main` function or helper functions called by `main`. For each step, add the code, save the file, and verify as instructed.

*   Exercise 1: Fixed Size Creation and Basic Access

    *   **Task:** Create a mutable fixed-size array of exactly 7 integers. Assign specific integer values directly within the array literal upon creation. Access and print the integer located at the first index (0) and the integer at the last index (index 6). Explain why the size had to be specified in the type signature or be inferred precisely.
    *   **Code Implementation/Hints:**
        *   Inside your `main` function, declare a mutable array. The syntax is `let mut my_array: [i32; 7] = [...];`.
        *   Fill in the square brackets `[...]` with 7 integer values, separated by commas.
        *   Access elements using `my_array[index]`.
        *   Use `println!` to display the elements at index 0 and index 6.
    *   **Explanation:** Arrays in Rust have their size fixed and known *at compile time*. This size is part of the array's type signature (`[T; N]`). The compiler needs to know the exact size to allocate the correct amount of memory on the stack. If you don't explicitly write the type signature, the compiler must be able to *infer* the size from the number of elements you provide in the array literal. If the number of elements doesn't match an explicit size, or if it's ambiguous, it's a compile-time error.
    *   **Verification:** Save `src/main.rs`. Run `cargo check`. It should compile. Run `cargo run`. Your terminal output should show the values you placed at indices 0 and 6.

*   Exercise 2: Mutability and Indexed Assignment

    *   **Task:** Given an existing mutable array of characters, change the character located at an intermediate index (not the first or last). Verify the change by printing the character at that index before and after your modification.
    *   **Code Implementation/Hints:**
        *   Create a mutable array of characters, e.g., `let mut chars: [char; 5] = ['H', 'e', 'l', 'l', 'o'];`.
        *   Pick an intermediate index (e.g., index 2).
        *   Print the character at that index before modification.
        *   Assign a new character to that index using bracket notation and the assignment operator: `chars[index] = 'X';`.
        *   Print the character at that index again after modification.
    *   **Explanation:** Since the array is declared as `mut`, you can modify its elements individually by accessing them via their index. The type of the element (`char` in this case) must remain the same.
    *   **Verification:** Save and run `cargo run`. The output should clearly show the original character followed by the new character at the chosen index.

*   Exercise 3: Efficient Initialization with Repetition

    *   **Task:** Create an array of 250 floating-point numbers, where every number is initialized to the value `0.0` using the array repetition syntax (`[value; count]`). Create another array of 100 empty strings using the same syntax (`[""; 100]`). Print the size (length) of both arrays.
    *   **Code Implementation/Hints:**
        *   Use the syntax `let array_name: [f64; 250] = [0.0; 250];`.
        *   Use similar syntax for the array of empty strings: `let string_array: [&str; 100] = [""; 100];`. Note that `""` is a string slice (`&str`), which is `Copy`, so this works. Using `String::from("")` inside the repetition syntax `[String::from(""); 100]` would *not* work because `String` is not `Copy`.
        *   Use `.len()` method on each array to get its size and print the result.
    *   **Explanation:** The repetition syntax `[value; count]` is a convenient and efficient way to create arrays where all elements have the same initial value. This value must be `Copy` for this syntax to work, because Rust needs to be able to duplicate the value `count` times without complex ownership transfers. An empty string slice (`&str`) is `Copy`.
    *   **Verification:** Save and run `cargo run`. The output should show the lengths 250 and 100.

*   Exercise 4: Iteration over Immutable Arrays

    *   **Task:** Create an array of boolean values. Iterate over this array using the `.iter()` method and print the index and the boolean value for each element in the loop. Explain what type `.iter()` yields references to.
    *   **Code Implementation/Hints:**
        *   Create an array of booleans: `let flags: [bool; 3] = [true, false, true];`.
        *   Use a `for` loop with `.iter()` and `.enumerate()`: `for (index, value_ref) in flags.iter().enumerate() { ... }`.
        *   Print `index` and `*value_ref`. Remember to dereference `value_ref` to get the actual boolean value, or print the reference directly (`value_ref`).
    *   **Explanation:** The `.iter()` method on an array provides an *iterator* that yields **immutable references** (`&T`) to the array's elements. This allows you to read the elements without taking ownership or requiring the array to be mutable. `.enumerate()` is an iterator adaptor that pairs each element with its index.
    *   **Verification:** Save and run `cargo run`. The output should list each index and the corresponding boolean value from the array.

*   Exercise 5: Iteration over Mutable Arrays

    *   **Task:** Create a mutable array of integers. Iterate over this array using the `.iter_mut()` method. Inside the loop, if the element's value is even, add 1 to it using the mutable reference; otherwise, subtract 1. After the loop, print the array's contents to confirm the changes.
    *   **Code Implementation/Hints:**
        *   Create a mutable array of integers: `let mut numbers: [i32; 4] = [1, 2, 3, 4];`.
        *   Use a `for` loop with `.iter_mut()`: `for num_ref in numbers.iter_mut() { ... }`. `num_ref` will be of type `&mut i32`.
        *   Inside the loop, use the dereference operator `*` to access and modify the value through the mutable reference: `*num_ref += 1;` or `*num_ref -= 1;`. You can use the modulo operator `%` to check for evenness (`*num_ref % 2 == 0`).
        *   After the loop, print the entire `numbers` array using debug formatting `{:?}` in `println!`.
    *   **Explanation:** The `.iter_mut()` method provides an iterator that yields **mutable references** (`&mut T`) to the array's elements. This is the standard way to iterate over an array and modify its contents in place without needing to access elements by index, which can be prone to out-of-bounds errors. You must dereference the mutable reference (`*num_ref`) to interact with the actual value it points to.
    *   **Verification:** Save and run `cargo run`. The output should show the array with its values modified (e.g., `[2, 3, 4, 5]`).

*   Exercise 6: Slicing Arrays

    *   **Task:** Create a numerical array with at least 10 elements. Create an immutable slice that includes elements from the 3rd element (index 2) up to, but not including, the 7th element (index 6). Create another slice that includes elements from the 5th element (index 4) until the end of the array. Iterate over one of your created slices and print its elements. Explain how slicing provides a "view" into the array without copying data.
    *   **Code Implementation/Hints:**
        *   Create a numerical array: `let data: [i32; 10] = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];`.
        *   Create the first slice using range syntax: `let slice1 = &data[2..6];`. This creates a slice of type `&[i32]`.
        *   Create the second slice: `let slice2 = &data[4..];`. The `..` without an end index means "until the end".
        *   Iterate over `slice1` using a `for` loop (slices are iterable): `for element in slice1 { ... }`.
    *   **Explanation:** A slice (`&[T]`) is a dynamic view into a contiguous sequence of elements in a collection like an array or vector. It is a reference that contains a pointer to the first element and the length of the slice. Crucially, **slicing does not copy the underlying data** of the array; it only creates a new pointer and length metadata on the stack. This makes slicing very efficient. You can create mutable slices (`&mut [T]`) using `.iter_mut()` or range notation on a mutable array (`&mut data[..]`).
    *   **Verification:** Save and run `cargo run`. The output should show the elements of the slice you iterated over (e.g., 3, 4, 5, 6 if you iterated over `slice1`).

*   Exercise 7: Understanding Array Copy Semantics (Copy Type Elements)

    *   **Task:** Create an array of `f64` numbers (a `Copy` type). Write a function that takes a fixed-size array `[f64; N]` by value (i.e., the function signature is `fn process_array(arr: [f64; 5]) { ... }`). Pass your `f64` array from `main` to this function. After the function call returns in `main`, attempt to print the *original* array. Explain why this action is permitted and what happened memory-wise when the array was passed to the function.
    *   **Code Implementation/Hints:**
        *   Create a mutable array in `main`: `let mut double_array: [f64; 5] = [1.1, 2.2, 3.3, 4.4, 5.5];`. (Using `mut` isn't strictly necessary for this exercise, but good practice).
        *   Define a separate function `fn process_array(arr: [f64; 5]) { ... }`. Inside this function, perhaps print one of the elements.
        *   Call the function from `main`: `process_array(double_array);`.
        *   After the function call, add a `println!` statement in `main` to print `double_array` using debug formatting `{:?}`.
    *   **Explanation:** Arrays whose element type `T` implements the `Copy` trait (like `i32`, `f64`, `bool`, `char`, and arrays/tuples of `Copy` types) *also* implement `Copy`. When you pass such an array to a function *by value* (`fn some_func(arr: [T; N])`), the entire array contents are copied bit-for-bit onto the function's stack frame. The original array in the caller's scope (`main`) remains valid and independent because its value was duplicated, not moved.
    *   **Verification:** Save and run `cargo run`. The program should compile and run without errors. The output will show the original array being printed *after* the function call, demonstrating it was not moved.

*   Exercise 8: Understanding Array Copy Semantics (Non-Copy Type Elements)

    *   **Task:** Create an array of `String`s (a non-`Copy` type). Write a function that attempts to take a fixed-size array `[String; N]` by value. Attempt to pass your `String` array from `main` to this function. Describe the compile-time error that occurs and explain *why* Rust prevents this operation for arrays containing non-`Copy` types, relating it back to the `Copy` trait and ownership.
    *   **Code Implementation/Hints:**
        *   Create an array of `String`s in `main`: `let string_array: [String; 2] = [String::from("first"), String::from("second")];`.
        *   Define a separate function `fn process_string_array(arr: [String; 2]) { ... }`. Inside this function, you could try printing an element.
        *   Attempt to call the function from `main`: `process_string_array(string_array);`.
        *   Observe the compile error.
    *   **Explanation:** Arrays whose element type `T` does *not* implement the `Copy` trait (like `String`, `Vec`, `Box`, and arrays/tuples containing non-`Copy` types) also do **not** implement `Copy`. When you attempt to pass such an array to a function *by value*, Rust tries to apply its default **move** semantics. However, the entire array must be moved atomically. If the element type is not `Copy`, moving the whole array would involve moving ownership of the resources managed by each element (`String`'s heap data). Rust's rules for moving array elements individually are complex and designed to prevent partial moves. Because of this complexity and the goal of preventing soundness issues, Rust currently does not allow moving (or implicitly copying) an entire array if its element type is not `Copy`. You must iterate and move/clone elements individually, or pass a reference/slice (`&[String]` or `&mut [String]`), or explicitly clone the array (if the element type is `Clone`). The compile error will clearly state that the array type doesn't implement the `Copy` trait.
    *   **Verification:** Save the file. Run `cargo check` or `cargo run`. The compiler should produce an error. Note down or describe the error message.

*   Exercise 9: Comparing Arrays

    *   **Task:** Create two arrays of the same fixed size and containing the exact same sequence of integer values. Create a third array of the same fixed size but with at least one different value. Use Rust's standard comparison operators (`==`, `!=`) to compare the arrays for equality and print whether each comparison evaluates to true or false. Explain the conditions under which two arrays are considered equal in Rust.
    *   **Code Implementation/Hints:**
        *   Create the three arrays, e.g.:
            ```rust
            let array1: [i32; 3] = [1, 2, 3];
            let array2: [i32; 3] = [1, 2, 3];
            let array3: [i32; 3] = [1, 2, 4];
            ```
        *   Use `println!` and the `==` and `!=` operators to compare `array1` with `array2`, and `array1` with `array3`.
    *   **Explanation:** In Rust, two arrays (`[T; N]`) can be compared for equality using `==` and inequality using `!=` *if* their element type `T` implements the `PartialEq` trait (which most primitive types like `i32` do) and they have the *exact same fixed size* `N`. The comparison is performed element by element: the arrays are equal if and only if they have the same length and all corresponding elements are equal (`array1[i] == array2[i]` for all valid `i`). Arrays of different sizes (`[i32; 3]` vs `[i32; 4]`) cannot be compared directly for equality this way at compile time because their types are different.
    *   **Verification:** Save and run `cargo run`. The output should print `true` for the comparison of the two identical arrays and `false` for the comparison with the different array.

Running the Application / Testing

To see the output of all the exercises you've completed (except for the compile error in Exercise 8), navigate to the `rust_arrays_lab` directory in your terminal and run:

```bash
cargo run
```

The expected output will be the sequence of print statements from each exercise (1, 2, 3, 4, 5, 6, 7, 9). Exercise 8 is expected to produce a compile-time error message when you run `cargo check` or `cargo run`.

You can also use `cargo check` periodically as you complete each step to quickly verify syntax without running the program.

Key Concepts Review

*   **Fixed Size (`[T; N]`)**: Arrays have a size (`N`) determined at compile time and cannot grow or shrink. This size is part of their type.
*   **Stack Allocation**: Arrays (especially smaller ones) are often allocated on the stack, offering performance benefits and predictable memory usage.
*   **Indexing (`array[index]`)**: Elements can be accessed and modified (if the array is mutable) using zero-based indexing within square brackets. Bounds checking occurs at runtime.
*   **Repetition Syntax (`[value; count]`)**: An efficient way to initialize arrays with repeated values. Requires the element type to be `Copy`.
*   **Iterators (`.iter()`, `.iter_mut()`)**:
    *   `.iter()` provides an iterator yielding immutable references (`&T`).
    *   `.iter_mut()` provides an iterator yielding mutable references (`&mut T`), allowing in-place modification.
*   **Slicing (`&[T]`, `&mut [T]`)**: Creating references to a contiguous portion of an array (or other collections). Slices offer a dynamic view without copying data.
*   **Copy/Move Semantics**:
    *   Arrays with `Copy` elements are themselves `Copy` and are copied bitwise when assigned or passed by value.
    *   Arrays with non-`Copy` elements are **not** `Copy` and cannot be implicitly moved (assigned or passed by value) as a whole. This prevents issues with resource management and partial moves.
*   **Comparison (`==`, `!=`)**: Arrays of the same size with `PartialEq` elements can be compared element by element.

(Optional) Challenges / Next Steps

1.  **Array to Vector Conversion:** Explore the `.to_vec()` method on arrays or slices. Create an array, create a slice from it, and convert both the array and the slice into `Vec<T>`s. Print their contents and types.
2.  **Using `array_init`:** For initializing arrays with complex logic where the element type is not `Copy`, the `array-init` crate ([https://crates.io/crates/array-init](https://crates.io/crates/array-init)) can be useful. Add it as a dependency and use its `array_init` function to create an array where each element's value depends on its index.
3.  **Array vs. Vector Trade-offs:** Write down scenarios where you would prefer an array over a vector, and vice versa. Consider performance, flexibility, and knowledge of size at compile time.
4.  **More Complex Slicing:** Experiment with ranges (`..`, `start..`, `..end`, `start..end`, `..=end`) and creating multiple overlapping or non-overlapping slices from the same array.

(Optional) Troubleshooting

*   **Index Out of Bounds Panic:** Accessing an element using `array[index]` where `index` is outside the valid range (`0` to `length - 1`) will cause a runtime panic. Be mindful of array sizes and indices. Using iterators (`.iter()`, `.iter_mut()`) or slicing can help avoid this.
*   **Type Mismatch Errors:** Ensure all elements in an array literal are of the same type (`[i32; 3] = [1, 2, 3];` - all are `i32`).
*   **Cannot Assign to Immutable Array/Slice:** If you get an error like `cannot assign to data in an immutable array`, ensure the array was declared with `mut` and you are using `.iter_mut()` or a mutable slice (`&mut [...]`) when attempting modification.
*   **Move/Copy Errors with Non-Copy Arrays:** As seen in Exercise 8, attempting to pass an array containing non-`Copy` types by value will result in a compile error indicating the trait bound `Copy` is not satisfied. You must pass references (`&[]`, `&mut []`) or clone elements explicitly if you need separate mutable copies.
*   **Debugging:** Use `dbg!(variable_name)` to print the value and type of variables at runtime, which can be helpful for inspecting array and slice contents.

Conclusion

You have successfully worked through various exercises demonstrating the core features and behaviors of arrays (`[T; N]`) in Rust. You've learned how their fixed size impacts creation and memory allocation, how to access and modify elements, how to iterate over them, and importantly, how their interaction with Rust's ownership and the `Copy` trait affects how they are handled during assignments and function calls compared to non-`Copy` types like `String` or `Vec`. This knowledge is essential for choosing the appropriate collection type for your data in Rust.

Remember to commit the final state of your project to your Git repository using `git commit` and push your changes (`git push`). Adopting Conventional Commits will help keep your project history clean and easy to navigate.

(Optional) Final Solution Code

Here is the complete code for `src/main.rs` containing solutions for all completed exercises:

```rust
// Final Code for src/main.rs

use std::string::String; // We need String for Exercise 8


// Helper function for Exercise 7
fn process_f64_array(arr: [f64; 5]) {
    println!("Inside process_f64_array: First element is {}", arr[0]);
    // 'arr' is a copy of the array passed from main, it's dropped here.
}

// Helper function for Exercise 8 (will cause a compile error if called with a non-Copy array)
// fn process_string_array(arr: [String; 2]) {
//     println!("Inside process_string_array: First element is {}", arr[0]);
//     // 'arr' would own the array elements here, they would be dropped when the function returns.
//     // But this function signature is effectively disallowed by the compiler for non-Copy element arrays.
// }


fn main() {
    println!("--- Exercise 1: Fixed Size Creation and Basic Access ---");
    // Task: Create a mutable fixed-size array of exactly 7 integers. Assign values directly.
    // Access and print elements at index 0 and index 6.
    let mut my_array: [i32; 7] = [10, 20, 30, 40, 50, 60, 70];
    println!("Array: {:?}", my_array);
    println!("First element (index 0): {}", my_array[0]);
    println!("Last element (index 6): {}", my_array[6]);
    // Explanation: Size [i32; 7] is fixed at compile time. Compiler infers 7 from elements.

    println!("\n--- Exercise 2: Mutability and Indexed Assignment ---");
    // Task: Change an element in a mutable char array by index. Print before and after.
    let mut chars: [char; 5] = ['H', 'e', 'l', 'l', 'o'];
    let index_to_change = 2;
    println!("Chars array before change: {:?}", chars);
    println!("Character at index {} before change: {}", index_to_change, chars[index_to_change]);
    chars[index_to_change] = 'p';
    println!("Chars array after change: {:?}", chars);
    println!("Character at index {} after change: {}", index_to_change, chars[index_to_change]);

    println!("\n--- Exercise 3: Efficient Initialization with Repetition ---");
    // Task: Create arrays using [value; count] syntax. Print their lengths.
    let zero_doubles: [f64; 250] = [0.0; 250];
    let empty_strings: [&str; 100] = [""; 100]; // &str is Copy

    println!("Size of zero_doubles array: {}", zero_doubles.len());
    println!("Size of empty_strings array: {}", empty_strings.len());
    // Explanation: Repetition syntax works because 0.0 (f64) and "" (&str) are Copy types.

    println!("\n--- Exercise 4: Iteration over Immutable Arrays ---");
    // Task: Iterate over a bool array using .iter().enumerate(). Print index and value.
    let flags: [bool; 3] = [true, false, true];
    println!("Iterating over flags array:");
    for (index, value_ref) in flags.iter().enumerate() {
        println!("Index: {}, Value: {}", index, *value_ref); // Or just value_ref since bool is Display
    }
    // Explanation: .iter() yields immutable references (&bool) to elements.

    println!("\n--- Exercise 5: Iteration over Mutable Arrays ---");
    // Task: Iterate over a mutable i32 array using .iter_mut(). Modify elements in place. Print array.
    let mut numbers: [i32; 4] = [1, 2, 3, 4];
    println!("Numbers array before mutable iteration: {:?}", numbers);
    for num_ref in numbers.iter_mut() {
        if *num_ref % 2 == 0 { // Check if the value is even
            *num_ref += 1; // Add 1 using mutable reference
        } else {
            *num_ref -= 1; // Subtract 1 using mutable reference
        }
    }
    println!("Numbers array after mutable iteration: {:?}", numbers);
    // Explanation: .iter_mut() yields mutable references (&mut i32) allowing modification.

    println!("\n--- Exercise 6: Slicing Arrays ---");
    // Task: Create slices from a numerical array. Iterate over a slice.
    let data: [i32; 10] = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
    let slice1 = &data[2..6]; // Elements at indices 2, 3, 4, 5
    let slice2 = &data[4..];  // Elements at indices 4, 5, 6, 7, 8, 9
    println!("Original array: {:?}", data);
    println!("Slice 1 (&data[2..6]): {:?}", slice1);
    println!("Slice 2 (&data[4..]): {:?}", slice2);

    println!("Iterating over Slice 1:");
    for element in slice1 {
        println!("Element: {}", element);
    }
    // Explanation: Slices (&[T]) are references to a part of the array, they don't copy data.

    println!("\n--- Exercise 7: Understanding Array Copy Semantics (Copy Type Elements) ---");
    // Task: Create an f64 array. Pass it by value to a function. Print original array afterwards.
    let double_array: [f64; 5] = [1.1, 2.2, 3.3, 4.4, 5.5];
    println!("Original double_array before function call: {:?}", double_array);
    process_f64_array(double_array); // Pass the array by value (it's Copy)
    println!("Original double_array after function call: {:?}", double_array); // Original is still valid
    // Explanation: [f64; 5] is Copy because f64 is Copy. Passing by value copies the entire array onto the function stack.

    println!("\n--- Exercise 8: Understanding Array Copy Semantics (Non-Copy Type Elements) ---");
    // Task: Create a String array. Attempt to pass it by value to a function. Note the compile error.
    let string_array: [String; 2] = [String::from("first"), String::from("second")];
    println!("Created string_array: {:?}", string_array);

    // Attempting to call process_string_array(string_array) here
    // would cause a compile error because [String; 2] is NOT Copy.
    // Remove the comment below and run cargo check to see the error:
    // process_string_array(string_array);
    // println!("string_array after function call (would fail): {:?}", string_array);

    println!("Note: Attempting to pass an array of non-Copy types (String) by value results in a compile error.");
    // Explanation: [String; 2] is NOT Copy because String is NOT Copy.
    // Rust prevents moving the entire array by value directly because it would involve
    // moving multiple non-Copy elements, which is disallowed to prevent issues.
    // You must pass a reference (&[String] or &mut [String]) or clone elements.

    println!("\n--- Exercise 9: Comparing Arrays ---");
    // Task: Compare arrays using == and !=. Print results.
    let array1: [i32; 3] = [1, 2, 3];
    let array2: [i32; 3] = [1, 2, 3];
    let array3: [i32; 3] = [1, 2, 4];

    println!("array1: {:?}", array1);
    println!("array2: {:?}", array2);
    println!("array3: {:?}", array3);

    println!("array1 == array2: {}", array1 == array2); // True
    println!("array1 != array2: {}", array1 != array2); // False
    println!("array1 == array3: {}", array1 == array3); // False
    println!("array1 != array3: {}", array1 != array3); // True
    // Explanation: Arrays of the same size with PartialEq elements are compared element-wise.

    println!("\n--- Lab Complete ---");
}
```

Check Questions (To Test Understanding)

1.  What are the two main ways to specify the size of a fixed-size array in Rust?
2.  When iterating over an array, what is the difference between using `.iter()` and `.iter_mut()` in terms of the types of references they yield and what you can do with those references inside the loop?
3.  What is an array slice (`&[T]`), and how does creating a slice differ from creating a new array in terms of memory allocation?
4.  Explain why an array like `[i32; 5]` implements the `Copy` trait, but an array like `[String; 5]` does not.
5.  Under what conditions can you directly compare two arrays for equality using the `==` operator?

Detailed Answers to Check Questions

1.  The two main ways to specify the size of a fixed-size array are:
    *   Explicitly in the type signature: `let my_array: [i32; 7] = [...];`
    *   Allowing the compiler to infer the size from the number of elements in the array literal: `let my_array = [10, 20, 30, 40, 50, 60, 70];` (Here, the compiler infers the type is `[i32; 7]`).
2.  `.iter()` yields **immutable references** (`&T`) to the array's elements. You can read the values through these references, but you cannot modify the elements they point to. `.iter_mut()` yields **mutable references** (`&mut T`) to the array's elements. You can read *and* modify the values through these references (by dereferencing them with `*`).
3.  An array slice (`&[T]`) is a **reference** to a contiguous sequence of elements within another collection, like an array or vector. It consists of a pointer to the first element of the slice and its length. Creating a slice **does not copy** the underlying data; it only creates this small reference structure on the stack. Creating a new array, on the other hand, involves allocating memory for the entire array and copying or initializing all its elements.
4.  An array like `[i32; 5]` implements the `Copy` trait because its element type, `i32`, implements `Copy`. A type can be `Copy` if all its components are `Copy` and it doesn't have a custom `Drop` implementation. `i32` is a primitive, fixed-size value with no resource management. An array like `[String; 5]` does **not** implement the `Copy` trait because its element type, `String`, does **not** implement `Copy` (since `String` manages heap memory and requires cleanup via `Drop`). If an array contains any non-`Copy` elements, the entire array cannot be `Copy`.
5.  You can directly compare two arrays for equality using `==` if and only if:
    *   They have the **exact same fixed size** (`N`). Their types must be identical (`[T; N]`).
    *   Their element type (`T`) implements the `PartialEq` trait (which is true for most built-in types).
    If these conditions are met, the `==` operator compares the arrays element by element.

---

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025. All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech