# Lab 15: Just Closures...

Closures are a powerful feature in Rust that allow you to create anonymous functions you can save in a variable or pass as arguments to other functions. Unlike regular functions, closures can "capture" values from the scope in which they are defined, providing flexibility and conciseness, particularly when working with iterators. This lab focuses on understanding how to define and use closures and how they interact with their environment through variable capture.

By the end of this lab, you will be able to:

*   Define and use simple closures.
*   Use closures with common iterator methods like `sort_by_key` and `filter`.
*   Understand the difference between a closure capturing by borrow (`&` or `&mut`) and capturing by move (ownership).
*   Choose the appropriate capture mechanism for different scenarios.

This lab will provide hands-on experience using closures in practical scenarios like sorting and filtering, highlighting their integration with Rust's standard library.

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`, structs, `Vec`).
    *   Familiarity with the concept of traits.
    *   A basic understanding of iterators is helpful.
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. Check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial closures lab setup`, `feat: complete sort_by_key exercise`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new closures_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd closures_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `closures_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file. You will define a `Person` struct outside of `main`.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file, unless otherwise specified. Use comments (`//`) to separate and label the code for each exercise.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`, unless otherwise specified.

1.  **Sorting with `sort_by_key` and Closures**
    Use closures to define custom sorting criteria for a vector of structs. The `sort_by_key` method takes a closure that maps each element to a key, and the vector is sorted based on these keys.

    *   **Exercise Type Goal:** Use closures with a standard library method (`sort_by_key`) for defining sorting logic.
    *   Define a struct `Person` *outside* of `main` with `first_name` (`String`) and `last_name` (`String`) fields. Derive `Debug` so you can print instances.
    *   In `main`, create a mutable `Vec<Person>` and add at least 3 instances of `Person` with different first and last names. Print the initial vector.
    *   a) Use the `.sort_by_key()` method on the vector to sort the persons **by first name**. Pass a closure `|person| { ... }` that returns the person's first name. Print the vector after sorting.
    *   b) Use `.sort_by_key()` again to sort the persons **by last name**. Pass a closure that returns the person's last name. Print the vector after sorting.
    *   c) Use `.sort_by_key()` one more time to sort the persons **by last name, then by first name**. The key returned by the closure can be a tuple, and tuples are compared element by element. Return a tuple containing the last name and then the first name from your closure. Print the vector after sorting.
    *   **Hint:** The closure for `sort_by_key` receives an immutable reference (`&Person`) to each element. Access fields using the `.` operator.
    *   **Crucial Line Example (Sorting by key):**
        ```rust
        vector.sort_by_key(|person| {
            // Return the field or tuple to sort by
            // ...
        });
        ```
    *   **Verification:** Run `cargo run`. Observe the vector printed after each sort operation and confirm the order matches the specified criteria.

2.  **Filtering with `filter` and Closures (Consuming vs. Non-Consuming)**
    Use closures with the `filter` iterator method to select elements based on a condition. Demonstrate filtering in a way that consumes the original vector and a way that keeps it intact.

    *   **Exercise Type Goal:** Use closures with an iterator method (`filter`) and understand the difference between iterating by value (`into_iter`) and by reference (`iter`).
    *   In `main`, create a `Vec<i32>` with several numbers, including some even and odd numbers.
    *   **Consuming Version:** Use `.into_iter()` on the vector, then call `.filter()` with a closure `|number| { ... }` that returns `true` for even numbers. Collect the results into a *new* vector using `.collect::<Vec<_>>()`. Print the new vector of even numbers. After this, the original vector is moved into the iterator and cannot be used.
    *   **Non-Consuming Version:** Re-create the same initial vector of numbers. Use `.iter()` on the vector (gives references), then call `.filter()` with a closure `|number_ref| { ... }` that returns `true` for even numbers. Collect the results into a *new* vector (it will be a `Vec<&i32>`). Print the new vector. After this, the original vector is still usable.
    *   **Hint:** An integer `num` is even if `num % 2 == 0`. When iterating with `.iter()`, the closure receives a reference `&i32`, so you need to dereference it (`*number_ref`) to perform the modulo operation.
    *   **Crucial Line Example (Filter closure):**
        ```rust
        .filter(|number| {
            number % 2 == 0 // Filter for even numbers
        })
        ```
    *   **Verification:** Run `cargo run`. Observe the two resulting vectors of even numbers. Note the type of the vector in the non-consuming version (`Vec<&i32>`) and confirm the original vector is still usable after the non-consuming filter.

3.  **Closure Borrow Capture**
    Create a closure that immutably borrows a variable from its surrounding scope.

    *   **Exercise Type Goal:** Understand how closures can capture variables by immutable reference, and that the original variable remains usable.
    *   In `main`, declare a variable `minimum_value` of type `i32` and set it to a value (e.g., 10).
    *   Create a `Vec<i32>` with various numbers.
    *   Define a closure `is_above_minimum` that takes an `i32` number as input and returns `true` if the number is greater than `minimum_value`. The closure should use the `minimum_value` variable from the outer scope.
    *   Use the vector's iterator, `.filter()`, and your `is_above_minimum` closure to filter the vector for numbers above the minimum. Collect the results into a new vector and print it.
    *   After the filtering, print the original `minimum_value` variable.
    *   **Trap:** Trying to modify `minimum_value` from within the closure (you can't with an immutable borrow). If you needed to modify it, you'd need a mutable borrow capture, which has different constraints.
    *   **Discuss:** How did the closure get access to `minimum_value`? It implicitly captured it by immutable reference. Why is the original `minimum_value` still usable after the closure is used? Because the capture was only a borrow.
    *   **Verification:** Run `cargo run`. The filtered vector should contain only numbers greater than `minimum_value`, and the value of `minimum_value` itself should be printed afterwards.

4.  **Closure Move Capture**
    Create a closure that takes ownership of a variable from its surrounding scope using the `move` keyword.

    *   **Exercise Type Goal:** Understand how the `move` keyword forces a closure to take ownership of captured variables, and that the original variables are then unusable.
    *   In `main`, declare a `String` variable `status_message` (which is not `Copy`).
    *   Define a closure `process_status` using the **`move`** keyword before the arguments (`move || { ... }`). Inside the closure, print the `status_message`.
    *   Call the `process_status` closure.
    *   After calling the closure, try to print the original `status_message` variable.
    *   **Observe:** You should get a compile error.
    *   **Discuss:** Why did this fail? The `move` keyword explicitly forced the closure to take ownership of `status_message` when the closure was defined. The original `status_message` variable was moved into the closure's environment and is no longer valid after the closure (which effectively holds and potentially drops the variable) is used. This is crucial when the closure's lifetime might exceed the captured variable's original scope, or when the closure needs exclusive access or needs to give away ownership of the captured variable.
    *   **Verification:** Run `cargo check`. The compile error is the expected outcome. Comment out the line attempting to use `status_message` after the closure call to see it compile.

**Running the Application**

As you complete each exercise, run `cargo run` (or `cargo check` if a step is expected to cause a compile error) to verify your understanding and code correctness before moving to the next one.

**Key Concepts Review**

*   **Closures**: Anonymous functions defined inline, denoted by `|parameters| { body }`.
*   **Capture**: Closures can access variables from their surrounding scope (their "environment").
*   **Borrow Capture**: By default, closures capture variables they need by the minimum required borrow (`&` or `&mut`). If a variable is only read, it's an immutable borrow (`&`). If it's modified, it's a mutable borrow (`&mut`), requiring the variable to be mutable (`let mut`).
*   **Move Capture (`move` keyword)**: The `move` keyword before the closure parameters (`move |...| { ... }`) forces the closure to take **ownership** of any variables it captures. This is necessary when the closure might outlive the captured variable's original scope, or when the closure needs to move or mutate the captured variable even if it's not marked `mut` in the outer scope (though mutability *within* the closure still needs `mut`).
*   **Iterator Methods**: Standard library methods like `sort_by_key`, `filter`, `map`, etc., often take closures as arguments to customize behavior.
*   **`.iter()` vs `.into_iter()`**: `.iter()` yields shared references (`&T`), allowing the original collection to remain usable. `.into_iter()` yields owned values (`T`), consuming the original collection.

**Challenges / Next Steps**

1.  **Mutable Borrow Capture:** Create a scenario where a closure *must* capture a variable by mutable reference (e.g., incrementing a counter from inside a closure passed to an iterator that doesn't consume the closure on first call, like `for_each`). Experiment with the `mut` keyword for the closure variable (`let mut modifying_closure = ...`).
2.  **Closures and `Fn`, `FnMut`, `FnOnce` Traits:** Research the `Fn`, `FnMut`, and `FnOnce` traits. How do they relate to how a closure captures variables and how many times it can be called? Which trait does a closure implement based on its capture and usage?

**Troubleshooting**

*   **"cannot borrow `...` as mutable/immutable because ..."**: This means the closure is trying to capture a variable in a way that conflicts with an existing borrow or the variable's mutability. Review the borrowing rules and consider if a mutable borrow capture is needed or if the `move` keyword is required.
*   **"use of moved value: ..."**: You are trying to use a variable after its ownership was transferred into a closure using the `move` keyword.
*   **Closure type errors (`expected closure that implements `Fn`, found one that implements `FnMut` or `FnOnce`)**: This often happens when the closure captures variables mutably or by move, but the function you are passing it to expects a closure that only captures immutably and can be called multiple times (`Fn`). Or vice versa. Adjust the closure capture (`move`) or check the documentation for the method expecting the closure.

**Conclusion**

You have successfully worked through key concepts of Rust closures, understanding how they enable flexible, inline behavior and how they interact with their environment through borrowing and moving variable captures. Mastering closures is essential for leveraging Rust's powerful iterators and functional programming patterns.

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete closures lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Final Solution Code (src/main.rs)**

```rust
// src/main.rs

use std::fmt::Debug; // Needed for Debug trait for Person

fn main() {
    println!("--- Rust Closures Lab Solutions ---");

    // 1. Exercise: Sorting with sort_by and Closures
    println!("\n--- Exercise 1: Sorting with sort_by ---");
    // Struct Person defined below main

    let mut persons = vec![
        Person {
            first_name: String::from("Alice"),
            last_name: String::from("Smith"),
        },
        Person {
            first_name: String::from("Bob"),
            last_name: String::from("Johnson"),
        },
        Person {
            first_name: String::from("Charlie"),
            last_name: String::from("Smith"),
        },
        Person {
            first_name: String::from("Alice"),
            last_name: String::from("Johnson"),
        },
    ];

    println!("Initial persons vector: {:?}", persons);

    // a) Sort by first name
    // Using sort_by: closure takes two references (&Person, &Person) and returns Ordering
    persons.sort_by(|a, b| a.first_name.cmp(&b.first_name));
    println!("\nSorted by first name: {:?}", persons);

    // b) Sort by last name
    // Using sort_by: closure takes two references (&Person, &Person) and returns Ordering
    persons.sort_by(|a, b| a.last_name.cmp(&b.last_name));
    println!("\nSorted by last name: {:?}", persons);

    // c) Sort by last name then first name
    // Using sort_by and chaining comparisons with .then()
    persons.sort_by(|a, b| {
        a.last_name
            .cmp(&b.last_name) // Compare last names first
            .then(a.first_name.cmp(&b.first_name)) // If last names are equal, compare first names
    });
    println!("\nSorted by last name then first name: {:?}", persons);

    // 2. Exercise: Filtering with filter and Closures (Consuming vs. Non-Consuming)
    println!("\n--- Exercise 2: Filtering with filter ---");

    let numbers = vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
    println!("Original numbers vector: {:?}", numbers);

    // Consuming Version: Use into_iter()
    let numbers_clone_for_consuming = numbers.clone(); // Clone because 'numbers' is needed again
    let even_numbers_consuming: Vec<_> = numbers_clone_for_consuming
        .into_iter()
        .filter(|number| number % 2 == 0) // Closure receives i32
        .collect();
    println!("Even numbers (consuming): {:?}", even_numbers_consuming);
    // 'numbers_clone_for_consuming' is moved and cannot be used after this.

    // Non-Consuming Version: Use iter()
    let even_numbers_non_consuming: Vec<_> = numbers
        .iter() // Uses original 'numbers' vector
        .filter(|number_ref| *number_ref % 2 == 0) // Closure receives &i32, dereference to check
        .collect();
    println!(
        "Even numbers (non-consuming): {:?}",
        even_numbers_non_consuming
    );
    println!("Original numbers vector is still usable: {:?}", numbers);

    // 3. Exercise: Closure Borrow Capture
    println!("\n--- Exercise 3: Closure Borrow Capture ---");

    let minimum_value = 5; // Variable from outer scope
    let values_to_filter = vec![1, 7, 3, 15, 8, 4];
    println!("Values to filter: {:?}", values_to_filter);
    println!("Minimum value (for filtering): {}", minimum_value);

    // Define a closure that captures 'minimum_value' by immutable borrow
    // Filter's closure for .iter() receives &&i32.
    // We must accept &&i32. Then compare the inner value (**number_ref_ref) with minimum_value.
    let is_above_minimum = |number_ref_ref: &&i32| {
        // Closure receives &&i32
        **number_ref_ref > minimum_value // Dereference twice to get i32 value for comparison
    };

    let filtered_values: Vec<_> = values_to_filter
        .iter()
        .filter(is_above_minimum) // Pass the closure to filter
        .collect();

    println!("Filtered values (above minimum): {:?}", filtered_values);

    // Original 'minimum_value' is still usable because the closure only borrowed it immutably
    println!("Original minimum_value after capture: {}", minimum_value);

    // 4. Exercise: Closure Move Capture
    println!("\n--- Exercise 4: Closure Move Capture ---");

    let status_message = String::from("Processing complete."); // Non-Copy variable

    // Define a closure that takes ownership of 'status_message' using 'move'
    let process_status = move || {
        // 'move' keyword forces capture by value
        println!("Inside closure: {}", status_message); // Use the captured String
        // 'status_message' is now owned by this closure
    };

    process_status(); // Call the closure (consumes the closure if FnOnce, might consume captured variable)

    // The original 'status_message' variable is moved into the closure's environment
    // when the closure was created. It is no longer usable here.
    // The following line would cause a compile error:
    // println!("Original status_message after move capture: {}", status_message);

    println!(
        "Original status_message variable is moved into the closure and is unusable after its creation/use."
    );

    println!("\n--- Lab Complete ---");
}

// --- Struct Definition (outside main) ---

// Exercise 1 Struct (requires use std::fmt::Debug;)
#[derive(Debug)]
struct Person {
    first_name: String,
    last_name: String,
}

```

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

**Check Questions (To Test Understanding)**

1.  What is a closure in Rust, and how is it different from a regular function?
2.  When using `Vec::sort_by_key`, what does the closure passed to it receive as input, and what should it return?
3.  Explain the difference between capturing a variable by borrow and capturing by move in a closure.
4.  If a variable is captured by move into a closure, what happens to the original variable?
5.  When filtering a `Vec<i32>` for even numbers, why might you choose to use `.iter().filter(...)` instead of `.into_iter().filter(...)`?

**Detailed Answers to Check Questions**

1.  A closure is an anonymous function (a function defined without a name) that can be defined inline and assigned to a variable or passed as an argument. The key difference from a regular function is that closures can **capture** variables from the scope where they are defined (their "environment"), allowing them to access and use those variables in their body, whereas regular functions cannot capture their environment in this way.
2.  When using `Vec::sort_by_key(|k| ...)`, the closure receives an **immutable reference (`&T`)** to each element in the vector being sorted (where `T` is the vector's element type). The closure should **return a value** whose type implements the `Ord` trait (meaning it can be ordered), such as numbers, strings, tuples, etc. The vector is then sorted based on the order of these returned "key" values.
3.  **Capturing by borrow** means the closure gets a reference (`&` or `&mut`) to the variable from the outer scope. The original variable retains ownership, and its lifetime must be at least as long as the closure's use of the borrow. The original variable is typically still usable after the closure (provided the borrow's lifetime rules are met). **Capturing by move** (using the `move` keyword) means the closure takes **ownership** of the variable's value. The original variable is moved into the closure's environment and becomes unusable after the closure is defined (unless the original variable's type is `Copy`, in which case a copy is moved). Move capture is needed when the closure's lifetime exceeds the original variable's scope or when the closure needs to modify the variable in a way that requires ownership or exclusive access that cannot be provided by a borrow.
4.  If a variable is captured by move into a closure using the `move` keyword, ownership of the variable's value is transferred from the original variable to the closure's environment when the closure is *created*. The original variable is then **invalid** and cannot be used after that point (unless its type implements `Copy`, in which case a copy is moved, and the original remains usable).
5.  You might choose to use `.iter().filter(...)` instead of `.into_iter().filter(...)` when filtering a `Vec<i32>` because `.iter()` yields **shared references (`&i32`)** to the original elements. This means the original `Vec<i32>` remains intact and **usable** after the filtering operation finishes (e.g., you can iterate over it again, access elements by index, etc.). In contrast, `.into_iter()` yields **owned values (`i32`)** and **consumes** the original vector, making the original vector variable unusable after the operation.


---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025.**
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*

