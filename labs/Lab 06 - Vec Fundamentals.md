
# Lab 06: `Vec<T>` Fundamentals

Welcome to this hands-on lab exploring `Vec<T>` (vectors) in Rust. Unlike fixed-size arrays (`[T; N]`), vectors are growable lists. Their size is not known at compile time, and their data is stored on the heap. Vectors are one of the most commonly used collection types in Rust due to their flexibility.

Understanding vectors is fundamental to working with dynamic data in Rust. This lab will guide you through their creation, growth, shrinking, access methods, different iteration techniques, and memory characteristics, contrasting them with arrays where appropriate.

Primary Learning Objective: To understand the core features, flexibility, and heap-based nature of `Vec<T>` in Rust.

Concrete Outcomes: By the end of this lab, you will be able to:

*   Create vectors using both the `vec![]` macro and `Vec::new()`.
*   Add (`push`) and remove (`pop`) elements dynamically.
*   Access elements using indexing (`[]`) and safe methods (`.get()`, `.get_mut()`).
*   Understand the concepts of vector length (`.len()`) and capacity (`.capacity()`).
*   Iterate over vector elements using immutable (`.iter()`), mutable (`.iter_mut()`), and consuming (`.into_iter()`) iterators.
*   Create and use slices from vectors.
*   Understand the move semantics of vectors when passed by value.
*   Compare vectors for equality.

Scenario

In many real-world applications, the amount of data you need to process isn't known in advance. You might be reading lines from a file until the end is reached, collecting results from multiple concurrent operations, or building a list based on user input. In these situations, fixed-size arrays are impractical. Vectors (`Vec<T>`) are the ideal tool for dynamically collecting and managing such data. This lab simulates building foundational data collection and manipulation skills required for dynamic scenarios.

Prerequisites

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. This lab is based on Rust `edition = "2024"`. We assume the following tool versions: `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`. Ensure you have at least Rust 1.56.0 or later.
    *   A Text Editor or IDE: Such as VS Code with `rust-analyzer`, Sublime Text, Vim, Emacs, etc.
    *   Access to a Terminal or Command Prompt.
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`).
    *   Understanding of primitive types.
    *   An initial understanding of arrays, including `Copy` vs. non-`Copy` element types (as covered in the previous lab, for comparison purposes).
    *   Basic concepts of ownership, borrowing, and scopes will be helpful background, though the lab aims to illustrate these.
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
    cargo new rust_vector_lab --edition 2024
    ```
    This command creates a new `rust_vector_lab` directory with the necessary basic files (`Cargo.toml`, `src/main.rs`). We are using the `2024` edition of Rust.
3.  **Navigate into the project directory:**
    ```bash
    cd rust_vector_lab
    ```
4.  No external dependencies are required for this lab.
5.  **Commit often:** As you implement the steps of this lab, commit your changes frequently using `git commit`. This saves your progress and creates useful checkpoints. Consider using [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `feat(vector): initial project setup`, `feat(vector): added vec creation and push exercises`) for a clear commit history.

Lab Steps (Iterative Development)

Open `src/main.rs` in your text editor. You will write all the code for the exercises within the `main` function or helper functions called by `main`. You can structure them by adding comments for each exercise and placing the code and `println!` statements within them. Remember to save `src/main.rs` and run `cargo check` periodically, and `cargo run` to see your progress.

*   Exercise 1: Vector Creation (`vec![]`, `Vec::new`) and Adding Elements (`push`)

    *   **Task:** Create an empty mutable vector of integers using `Vec::new()`. Use the `.push()` method to add several integer values (e.g., 1, 2, 3) one by one. After each `push`, print the vector's contents using debug formatting `{:?}` and its current length using `.len()`. Then, create another vector of characters initialized directly using the `vec![]` macro with initial values (e.g., 'R', 'u', 's', 't'). Print this vector's contents and length. Explain the difference in initial creation syntax.
    *   **Explanation:** Vectors are dynamically sized, meaning their size can change at runtime. `Vec::new()` creates a vector with a length of 0, and typically a capacity of 0 initially. The `push` method appends elements. The `vec![]` macro provides a convenient way to initialize a vector with data upfront.
    *   **Code Implementation/Hints:**
        *   Start with `let mut my_vec_new: Vec<i32> = ...;`.
        *   Use `vector.push(value);` multiple times.
        *   Use `println!("...", vector, vector.len());` after each `push`.
        *   Use `let macro_vec: Vec<char> = vec![...];`.
    *   **Verification:** Run `cargo run`. Check that the printed vector contents and lengths correctly reflect the added elements.

*   Exercise 2: Mutability and Indexed Assignment (`[]`)

    *   **Task:** Given an existing mutable vector of floating-point numbers, change the value located at an existing index (e.g., index 1) using bracket notation (`[]`). Print the value at that index before and after your modification. Explain how this is similar to modifying array elements.
    *   **Explanation:** Just like arrays, you can directly access elements in a vector using their index if the vector is mutable. Be aware that `[]` does *not* perform bounds checking, potentially leading to a crash if the index is invalid.
    *   **Code Implementation/Hints:**
        *   Create a mutable vector like `let mut float_vec: Vec<f64> = vec![1.1, 2.2, 3.3, 4.4];`.
        *   Print the value at a specific index using `vector[index]`.
        *   Modify the value: `vector[index] = new_value;`.
        *   Print the value again after modification.
    *   **Verification:** Run `cargo run`. Confirm that the value at the specified index has changed in the output.

*   Exercise 3: Safe Indexed Access (`.get()`, `.get_mut()`)

    *   **Task:** Create a vector of integers. Use the `.get()` method to safely attempt to access an element at a valid index and another attempt at an invalid (out-of-bounds) index. Use `match` statements to handle the `Option` result of `.get()`, printing the value if present or an "index out of bounds" message otherwise. Repeat this process using `.get_mut()` on a mutable vector to safely get a mutable reference to an element at a *valid* index and modify its value within the `match` block, then attempt mutable access at an invalid index. Explain why `.get()` and `.get_mut()` are considered safer than using `[]` directly when the index is not guaranteed to be within bounds.
    *   **Explanation:** When you are unsure if an index is valid, using `[]` will panic if you're wrong. The `.get()` and `.get_mut()` methods return an `Option`, which is `Some` if the index is valid (containing a reference to the element) and `None` if it's invalid. Handling `None` allows your program to continue gracefully instead of crashing.
    *   **Code Implementation/Hints:**
        *   Create an immutable vector `let safe_vec = vec![10, 20, 30];`.
        *   Call `safe_vec.get(index)` for a valid and an invalid index.
        *   Use a `match` statement for the `Option`:
            ```rust
            match my_option {
                Some(value) => { /* print value */ },
                None => { /* print "out of bounds" */ },
            }
            ```
        *   Repeat with a `mut` vector and `.get_mut(index)`. Remember to dereference `value_ref` to modify: `*value_ref = new_value;`.
    *   **Verification:** Run `cargo run`. Observe that no panics occur. Check that valid accesses succeed and print values, and invalid accesses print the "out of bounds" message. Verify the element was modified correctly using `.get_mut()`.

*   Exercise 4: Efficient Initialization with Repetition (`vec![value; count]`)

    *   **Task:** Create a large vector of 1000 boolean values, initialized to `false`, using the repetition syntax (`vec![value; count]`). Create another vector of 50 owned strings (`Vec<String>`), initialized to empty strings, using the same syntax (`vec![value.clone(); count]` or `vec![Value::default(); count]` if Value has Default or the specific value is `Clone`). Print the length (`.len()`) and the estimated *capacity* (`.capacity()`) of both vectors after creation. Briefly explain capacity.
    *   **Explanation:** The repetition syntax is useful for vectors too. When working with types that aren't `Copy` but are `Clone` (like `String`), the value provided in the repetition must be able to be cloned. The vector allocates space based on the `count` but might allocate extra space upfront (capacity) to reduce future reallocations when you push more elements.
    *   **Code Implementation/Hints:**
        *   Use `let bool_vec: Vec<bool> = vec![false; 1000];`.
        *   For strings, remember `String::from("")` gives an owned `String`. `String` is `Clone`.
        *   Use `vector.len()` and `vector.capacity()`.
    *   **Verification:** Run `cargo run`. Check the reported lengths (1000 and 50). The capacity will be at least the length, potentially larger.

*   Exercise 5: Dynamic Growth and Shrinking (`push`, `pop`, `len`, `capacity`)

    *   **Task:** Create a mutable, empty vector of integers (`Vec<i32>`). Add three numbers using `push()`, printing the vector, length, and capacity after each `push`. Then, use the `.pop()` method once, print the returned value (the removed element, remember it's an `Option`), the vector's new contents, length, and capacity. Explain that `.capacity()` might not decrease immediately after `pop`.
    *   **Explanation:** Vectors manage a heap buffer. `push` increases length and might trigger reallocation to increase capacity. `pop` decreases length, but typically doesn't release the heap memory back immediately.
    *   **Code Implementation/Hints:**
        *   Start with `let mut dynamic_vec: Vec<i32> = Vec::new();`.
        *   Use `dynamic_vec.push(...)`.
        *   Print using `{:?}`, `.len()`, and `.capacity()`.
        *   Call `dynamic_vec.pop()`, handle the `Option` with `match`.
        *   Print details again after `pop`.
    *   **Verification:** Run `cargo run`. Follow the length and capacity changes in the output. Note how `capacity` stays the same after `pop`. Confirm `pop` returns the correct last element or `None` if empty.

*   Exercise 6: Iteration over Immutable Vectors (`.iter()`)

    *   **Task:** Create a vector of strings (`Vec<String>`). Iterate over this vector using the `.iter()` method and `.enumerate()`, printing the index and the *referenced* string (`&String`) for each element in the loop. Explain what type `.iter()` yields references to, just as you did for arrays.
    *   **Explanation:** Iterating with `.iter()` provides read-only access via references. The vector is only borrowed, so you can continue using it after the loop. This is identical behavior to `.iter()` on arrays.
    *   **Code Implementation/Hints:**
        *   Create `let words: Vec<String> = vec![...];`.
        *   Use `for (index, word_ref) in words.iter().enumerate() { ... }`. `word_ref` will be of type `&String`.
        *   Print `index` and `word_ref` (or `*word_ref`).
        *   Add a print statement *after* the loop that uses the original `words` vector.
    *   **Verification:** Run `cargo run`. The output should show the index and value of each element, and then successfully print the vector contents again after the loop.

*   Exercise 7: Iteration over Mutable Vectors (`.iter_mut()`)

    *   **Task:** Create a mutable vector of integers. Iterate over this vector using the `.iter_mut()` method. Inside the loop, multiply the element's value by 2 using the mutable reference (`&mut i32`). After the loop, print the vector's contents to confirm the changes, similar to the array exercise.
    *   **Explanation:** `.iter_mut()` provides mutable references, allowing you to modify elements directly within the loop. Like `.iter_mut()` on arrays, this takes a mutable borrow, ensuring unique access during iteration. The original vector variable remains valid.
    *   **Code Implementation/Hints:**
        *   Create `let mut multiplier_vec: Vec<i32> = vec![...];`.
        *   Use `for value_ref in multiplier_vec.iter_mut() { ... }`. `value_ref` is of type `&mut i32`.
        *   Use `*value_ref *= 2;`.
        *   Print the vector *before* and *after* the loop.
    *   **Verification:** Run `cargo run`. Output should show the elements of the vector doubled after the loop.

*   Exercise 8: Iteration and Moving Out (`.into_iter()`)

    *   **Task:** Create a vector of non-`Copy` type elements, such as `String` (`Vec<String>`). Iterate over this vector using the **`.into_iter()`** method. Inside the loop, print the string value (you will receive the `String` by value). After the loop, attempt to print the *original* vector using debug formatting `{:?}`. Describe the compile-time error or runtime behavior, and explain how `.into_iter()` affects the ownership of both the vector and its elements, contrasting it with `.iter()` and `.iter_mut()`.
    *   **Explanation:** The `.into_iter()` method consumes the vector, transferring ownership of the vector and yielding owned values of its elements. This is the most "destructive" form of iteration but is necessary if you need the actual owned values from the vector's elements.
    *   **Code Implementation/Hints:**
        *   Create `let consume_vec: Vec<String> = vec![...];`. Make sure these are `String`s (not `&str`).
        *   Use `for owned_string in consume_vec.into_iter() { ... }`. `owned_string` will be type `String`.
        *   Inside the loop, use `println!("Consumed element: {}", owned_string);`.
        *   Add a `println!("consume_vec after .into_iter(): {:?}", consume_vec);` *after* the loop and observe what the compiler says when you run `cargo check`.
    *   **Verification:** Run `cargo check`. Observe and understand the compile error preventing the use of the vector after `.into_iter()`. If you comment out the problematic line, the code will compile and run, demonstrating iteration by value.

*   Exercise 9: Slicing Vectors

    *   **Task:** Create a vector of numbers with at least 10 elements (e.g., numbers 0-9). Create an immutable slice from it including elements from index 5 to 9 (`[5..10]`). Create a mutable slice from a mutable vector including elements from the start up to index 4 (`[..5]`). Iterate over one of these slices (e.g., print elements of the immutable slice). Explain that slicing syntax and behavior is consistent between arrays and vectors because both provide a contiguous memory layout.
    *   **Explanation:** Both arrays and vectors store data contiguously. This common layout enables slices, which are lightweight views into the underlying data buffer. Slices borrow the data and don't copy it.
    *   **Code Implementation/Hints:**
        *   Create a vector: `let numbers_for_slice: Vec<i32> = (0..10).collect();`.
        *   Create mutable vector: `let mut mutable_slice_vec: Vec<char> = vec!['a', 'b', 'c', 'd', 'e', 'f', 'g'];`.
        *   Create immutable slice: `let my_slice: &[i32] = &numbers_for_slice[start..end];`.
        *   Create mutable slice: `let mut mutable_char_slice: &mut [char] = &mut mutable_slice_vec[..end];`.
        *   Use a `for` loop to iterate over `my_slice`.
    *   **Verification:** Run `cargo run`. The output should show the elements from the immutable slice. The code should compile without errors, confirming the validity of the slicing syntax.

*   Exercise 10: Vector Copy/Move Semantics (Passing by Value)

    *   **Task:** Create a vector of integers (`Vec<i32>`). Write a function that takes a `Vec<i32>` by value (`fn process_vec(v: Vec<i32>) { ... }`). Pass your `Vec<i32>` from `main` to this function. After the function call returns in `main`, attempt to print the *original* vector using debug formatting. Describe the compile-time error that occurs and explain *why* Rust prevents this, relating it to the `Vec`'s heap ownership and `Move` semantics. Contrast this behavior with passing a `Copy` array by value (as you did in the array lab).
    *   **Explanation:** `Vec<T>` is not a `Copy` type because it manages a heap allocation. Passing a vector by value results in a move of ownership of the vector structure (including the pointer to the heap data). The original variable becomes invalid.
    *   **Code Implementation/Hints:**
        *   Create `let move_vec: Vec<i32> = vec![10, 20, 30];` in `main`.
        *   Define `fn process_vec(v: Vec<i32>) { ... }`. Maybe print `v.len()` inside.
        *   Call `process_vec(move_vec);`.
        *   Add a `println!("move_vec after function call: {:?}", move_vec);` after the call and observe the compiler error.
    *   **Verification:** Run `cargo check`. Note down the specific compile error about the value being moved.

*   Exercise 11: Comparing Vectors (`==`)

    *   **Task:** Create two vectors with the same integer values in the same order. Create a third vector with the same integer values but in a different order, or with a different length, or with different values. Use Rust's standard comparison operators (`==`, `!=`) to compare the vectors for equality and print the boolean result. Explain when two vectors are considered equal, highlighting that it depends on both length and element values.
    *   **Explanation:** Vectors can be compared for equality using `==` and inequality using `!=` *if* their element type `T` implements `PartialEq`. Equality means both vectors have the *same length* AND all elements at corresponding positions are equal.
    *   **Code Implementation/Hints:**
        *   Create three `Vec<i32>` instances, two identical in value and order, one different.
        *   Use `println!("vector1 == vector2: {}", vector1 == vector2);`.
    *   **Verification:** Run `cargo run`. Output should show the expected boolean results of the comparisons.

Running the Application / Testing

To compile and run your completed lab code (commenting out the lines designed to cause compile errors), navigate to the `rust_vector_lab` directory in your terminal and run:

```bash
cargo run
```

To observe the compile errors, you can uncomment the lines causing them (e.g., in Exercise 8 or 10) and run `cargo check`.

Key Concepts Review

*   **Dynamic Size (`Vec<T>`)**: Vectors can grow and shrink at runtime. Their size is not fixed or known at compile time.
*   **Heap Allocation**: Vector data is stored on the heap, managed by the `Vec` structure on the stack (which holds a pointer, length, and capacity).
*   **Creation (`Vec::new()`, `vec![]`)**: Different ways to create empty or initialized vectors.
*   **Dynamic Modification (`push`, `pop`)**: Adding or removing elements at the end of the vector.
*   **Length vs. Capacity**: `len()` is the number of elements currently held, `capacity()` is the number of elements the vector can hold before requiring reallocation.
*   **Safe vs. Unsafe Indexing (`.get()`, `.get_mut()` vs. `[]`)**: Methods like `.get()` provide bounds checking via `Option<T>` to prevent panics from invalid indices, unlike the unchecked `[]`.
*   **Iteration Methods**:
    *   `.iter()`: Immutable borrowing (`&T`), vector still usable.
    *   `.iter_mut()`: Mutable borrowing (`&mut T`), vector still usable but mutably borrowed.
    *   `.into_iter()`: Consumes the vector (moves ownership), yields owned values (`T`), original vector variable invalidated.
*   **Slicing (`&[T]`, `&mut [T]`)**: Creating views into a vector's contiguous heap data without copying. Syntax is consistent with arrays.
*   **Move Semantics (Container)**: `Vec<T>` is not `Copy`. Assigning a vector variable or passing it by value results in a move of the vector structure (pointer, length, capacity), invalidating the original variable. This correctly transfers ownership of the heap buffer.
*   **Comparison (`==`, `!=`)**: Vectors can be compared if element type is `PartialEq`; equality requires same length and identical elements at corresponding positions.

(Optional) Challenges / Next Steps

1.  **Pre-allocate Capacity:** Use `Vec::with_capacity(n)` when you have an estimate of the final size to avoid multiple reallocations during `push` operations.
2.  **Removing Elements by Index:** Explore methods like `remove(index)`, `swap_remove(index)`, and `retain(|element| ...)` for removing elements at arbitrary positions or based on a condition. Note the performance implications (e.g., `remove` shifts subsequent elements, `swap_remove` is faster).
3.  **Vector of Option:** Create a `Vec<Option<i32>>` and explore pushing `Some(value)` and `None`. Iterate over it, handling both variants within the loop using `match` or `if let`.
4.  **Sort a Vector:** Add integers to a vector, then use the `.sort()` method to sort them. Print before and after sorting.

(Optional) Troubleshooting

*   **Index Out of Bounds Panic (`[]`)**: Remember that `vector[index]` panics if the index is invalid. Use `.get()` or `.get_mut()` for safer access when needed.
*   **`value used here after move` error**: This occurs when you try to use a vector variable after it has been moved, most commonly after passing it by value to a function (Exercise 10) or after calling `.into_iter()` (Exercise 8). The compiler prevents you from using the invalidated variable.
*   **Mutability Errors (`cannot borrow as mutable`)**: If you need to modify a vector itself (e.g., `push`, `pop`) or its elements (`iter_mut()`, `get_mut()`), ensure the vector variable is declared with `mut`.
*   **`cannot borrow` when using multiple slices**: You cannot have both a mutable borrow (`&mut`) and any other borrow (immutable `&`, mutable `&mut`) of the *same part* or *overlapping parts* of a vector at the same time. This is a core Rust borrowing rule enforced to prevent data races.
*   **Debugging:** Use `dbg!(variable)` to print the value and type of vector or slice variables at runtime to inspect their state.

Conclusion

You have successfully worked through numerous exercises on vectors (`Vec<T>`), the workhorse of dynamic collections in Rust. You now understand how to create, modify, and iterate over vectors using various methods, how their dynamic size impacts memory management (length and capacity), and crucially, how their non-`Copy` nature means they follow move semantics when assigned or passed by value, contrasting with `Copy` arrays. You also learned to use slices as flexible views. This comprehensive practice provides a strong foundation for using vectors effectively in your Rust programs.

Remember to commit the final state of your project to your Git repository using `git commit` and push your changes (`git push`). Adopting Conventional Commits will help keep your project history clean and easy to navigate.

(Optional) Final Solution Code

```rust
// Final Code for src/main.rs

use std::string::String;

// Helper function for Exercise 10
fn process_vec(v: Vec<i32>) { // Takes ownership of the vector
    println!("Inside process_vec function:");
    println!("Vector received (len {}): {:?}", v.len(), v);
    // 'v' goes out of scope here, dropping the vector and its elements.
}


fn main() {
    println!("--- Exercise 1: Vector Creation (vec![], Vec::new) and Adding Elements (push) ---");
    let mut my_vec_new: Vec<i32> = Vec::new();
    println!("my_vec_new after creation: {:?}, length: {}", my_vec_new, my_vec_new.len());
    my_vec_new.push(1);
    println!("my_vec_new after 1 push: {:?}, length: {}", my_vec_new, my_vec_new.len());
    my_vec_new.push(2);
    my_vec_new.push(3);
    println!("my_vec_new after more pushes: {:?}, length: {}", my_vec_new, my_vec_new.len());

    let macro_vec: Vec<char> = vec!['R', 'u', 's', 't'];
    println!("macro_vec: {:?}, length: {}", macro_vec, macro_vec.len());
    println!("Explanation: Vec::new() creates an empty vector; vec![] creates one with initial elements.");

    println!("\n--- Exercise 2: Mutability and Indexed Assignment ([]) ---");
    let mut float_vec: Vec<f64> = vec![1.1, 2.2, 3.3, 4.4];
    let index_to_change = 1;
    println!("float_vec before change: {:?}", float_vec);
    println!("Value at index {} before change: {}", index_to_change, float_vec[index_to_change]);
    float_vec[index_to_change] = 9.9; // Directly modify element by index
    println!("float_vec after change: {:?}", float_vec);
    println!("Value at index {} after change: {}", index_to_change, float_vec[index_to_change]);
    println!("Explanation: Mutating by index is similar to arrays for valid indices.");

    println!("\n--- Exercise 3: Safe Indexed Access (.get(), .get_mut()) ---");
    let mut safe_vec: Vec<i32> = vec![10, 20, 30];
    let valid_index = 1;
    let invalid_index = 5;

    println!("Attempting safe immutable access (.get):");
    match safe_vec.get(valid_index) {
        Some(value) => println!("Index {} is valid, value: {}", valid_index, value),
        None => println!("Index {} is out of bounds!", valid_index),
    }
    match safe_vec.get(invalid_index) {
         Some(value) => println!("Index {} is valid, value: {}", invalid_index, value),
         None => println!("Index {} is out of bounds!", invalid_index),
    }

    println!("Attempting safe mutable access (.get_mut):");
    match safe_vec.get_mut(valid_index) { // .get_mut requires a mutable vector
        Some(value_ref) => {
            println!("Index {} is valid for mutable access, original value: {}", valid_index, value_ref);
            *value_ref = 99; // Modify the value via the mutable reference
            println!("Value after modification: {}", safe_vec[valid_index]);
        },
        None => println!("Index {} is out of bounds for mutable access!", valid_index),
    }
     match safe_vec.get_mut(invalid_index) { // This match will hit the None arm
         Some(value_ref) => { println!("Index {} is valid for mutable access, original value: {}", invalid_index, value_ref); },
         None => println!("Index {} is out of bounds for mutable access!", invalid_index),
     }
    println!("Explanation: .get/.get_mut return Option, preventing panic on invalid index.");

    println!("\n--- Exercise 4: Efficient Initialization with Repetition (vec![value; count]) ---");
    let thousands_of_false: Vec<bool> = vec![false; 1000];
    println!("thousands_of_false vector: length = {}, capacity = {}",
             thousands_of_false.len(), thousands_of_false.capacity());

    // Use Vec<String> initialized with repetition. String is Clone.
    let fifty_empty_strings_owned: Vec<String> = vec![String::from(""); 50];
    println!("fifty_empty_strings_owned (String) vector: length = {}, capacity = {}",
             fifty_empty_strings_owned.len(), fifty_empty_strings_owned.capacity());
    println!("Explanation: vec![value; count] clones or copies 'value' count times.");
    println!("Capacity is pre-allocated space to avoid immediate reallocations on push.");


    println!("\n--- Exercise 5: Dynamic Growth and Shrinking (push, pop, len, capacity) ---");
    let mut dynamic_vec: Vec<i32> = Vec::new();
    println!("Initial: {:?}", dynamic_vec);
    println!("Len: {}, Cap: {}", dynamic_vec.len(), dynamic_vec.capacity());

    dynamic_vec.push(100);
    println!("After push(100): {:?}", dynamic_vec);
    println!("Len: {}, Cap: {}", dynamic_vec.len(), dynamic_vec.capacity());

    dynamic_vec.push(200);
    println!("After push(200): {:?}", dynamic_vec);
    println!("Len: {}, Cap: {}", dynamic_vec.len(), dynamic_vec.capacity());

    dynamic_vec.push(300);
    println!("After push(300): {:?}", dynamic_vec);
    println!("Len: {}, Cap: {}", dynamic_vec.len(), dynamic_vec.capacity());

    println!("Attempting pop():");
    let popped_item = dynamic_vec.pop(); // .pop() returns Option<T>
    match popped_item {
        Some(value) => println!("Popped value: {}", value),
        None => println!("Vector was empty! Nothing to pop."),
    }
    println!("After pop(): {:?}", dynamic_vec);
    println!("Len: {}, Cap: {}", dynamic_vec.len(), dynamic_vec.capacity());
    println!("Explanation: push grows, potentially increasing capacity. pop reduces length but not usually capacity.");


    println!("\n--- Exercise 6: Iteration over Immutable Vectors (.iter()) ---");
    let words: Vec<String> = vec![String::from("Hello"), String::from("from"), String::from("Vec")];
    println!("Iterating over words vector using .iter().enumerate():");
    for (index, word_ref) in words.iter().enumerate() { // word_ref is &String
        println!("Index: {}, Value reference: {}", index, word_ref);
    }
    println!("Original words vector still usable: {:?}", words);
    println!("Explanation: .iter() borrows immutably (&self), yielding immutable element references (&T).");


    println!("\n--- Exercise 7: Iteration over Mutable Vectors (.iter_mut()) ---");
    let mut multiplier_vec: Vec<i32> = vec![5, 10, 15, 20];
    println!("Multiplier vector before .iter_mut(): {:?}", multiplier_vec);

    println!("Multiplying values using .iter_mut():");
    for value_ref in multiplier_vec.iter_mut() { // value_ref is &mut i32
         *value_ref *= 2; // Dereference and modify
    }

    println!("Multiplier vector after .iter_mut(): {:?}", multiplier_vec);
    println!("Original multiplier_vec still usable: {:?}", multiplier_vec);
    println!("Explanation: .iter_mut() borrows mutably (&mut self), yielding mutable element references (&mut T).");


    println!("\n--- Exercise 8: Iteration and Moving Out (.into_iter()) ---");
    let consume_vec: Vec<String> = vec![String::from("alpha"), String::from("beta"), String::from("gamma")];
    println!("Iterating over consume_vec using .into_iter() (consumes vector):");
    for owned_string in consume_vec.into_iter() { // Takes ownership of consume_vec, yields String
         println!("Consumed element: {}", owned_string);
    }
    // The 'consume_vec' variable is now invalidated by the .into_iter() call.
    // Uncomment the next line to see the compile error:
    // println!("consume_vec after .into_iter(): {:?}", consume_vec); // COMPILE ERROR
    println!("Explanation: .into_iter() moves the vector (consumes it), yielding owned elements (T). Original variable is invalidated.");


    println!("\n--- Exercise 9: Slicing Vectors ---");
    let numbers_for_slice: Vec<i32> = (0..10).collect(); // Creates a vector with numbers 0 through 9
    let mut mutable_slice_vec: Vec<char> = vec!['a', 'b', 'c', 'd', 'e', 'f', 'g'];

    // Immutable slice from index 5 (inclusive) up to 10 (exclusive)
    let my_slice: &[i32] = &numbers_for_slice[5..10];

    // Mutable slice from start (inclusive) up to 5 (exclusive)
    let mut mutable_char_slice: &mut [char] = &mut mutable_slice_vec[..5];

    println!("Original vector for immutable slice: {:?}", numbers_for_slice);
    println!("Created slice (&[i32]): {:?}", my_slice);
    println!("Original vector for mutable slice: {:?}", mutable_slice_vec);
    println!("Created mutable slice (&mut [char]): {:?}", mutable_char_slice);

    println!("Iterating over the immutable slice:");
    for element in my_slice { // Slices are iterable
        println!("Slice element: {}", element);
    }
    println!("Explanation: Slices (&[T], &mut [T]) provide views into contiguous vector data without copying, syntax similar to arrays.");


    println!("\n--- Exercise 10: Vector Copy/Move Semantics (Passing by Value) ---");
    let move_vec: Vec<i32> = vec![10, 20, 30];
    println!("move_vec before function call: {:?}", move_vec);
    process_vec(move_vec); // Passing by value moves ownership
    // Uncomment the next line to see the compile error:
    // println!("move_vec after function call: {:?}", move_vec); // COMPILE ERROR
    println!("Explanation: Vec<T> is NOT Copy due to heap ownership. Passing by value moves ownership, invalidating the original.");


    println!("\n--- Exercise 11: Comparing Vectors (==) ---");
    let vector1: Vec<i32> = vec![1, 2, 3];
    let vector2: Vec<i32> = vec![1, 2, 3]; // Identical
    let vector3: Vec<i32> = vec![1, 2, 4]; // Same size, different element
    let vector4: Vec<i32> = vec![1, 3, 2]; // Same size, different order
    let vector5: Vec<i32> = vec![1, 2];    // Different size

    println!("vector1: {:?}", vector1);
    println!("vector2: {:?}", vector2);
    println!("vector3: {:?}", vector3);
    println!("vector4: {:?}", vector4);
    println!("vector5: {:?}", vector5);


    println!("vector1 == vector2: {}", vector1 == vector2); // true (same length, same elements)
    println!("vector1 != vector2: {}", vector1 != vector2); // false

    println!("vector1 == vector3: {}", vector1 == vector3); // false (same length, different element)
    println!("vector1 == vector4: {}", vector1 == vector4); // false (same length, different order)
    // Comparison with vector5 (different length) works and returns false
    println!("vector1 == vector5: {}", vector1 == vector5); // false (different length)
    println!("Explanation: Vectors are equal if they have the same length AND same elements in the same order.");


    println!("\n--- Lab Complete ---");
}
```

Check Questions (To Test Understanding)

1.  What is the key difference between an array (`[T; N]`) and a vector (`Vec<T>`) in terms of their size?
2.  In vector terminology, what is the difference between "length" (`len()`) and "capacity" (`capacity()`)?
3.  Why are `.get(index)` and `.get_mut(index)` methods on `Vec` generally preferred over using the `[]` operator when dealing with potentially invalid indices?
4.  Explain the primary difference between iterating over a vector using `.iter()` versus using `.into_iter()`. How does this relate to Rust's ownership system?
5.  When passing a `Vec<T>` to a function **by value**, does the vector get copied or moved? What are the implications of this behavior for the original vector variable after the function call returns?

Detailed Answers to Check Questions

1.  An array (`[T; N]`) has a **fixed size** (`N`) that is determined and known **at compile time**. This size cannot change during the program's execution. A vector (`Vec<T>`), on the other hand, is a **dynamically sized** collection. Its size (the number of elements it contains) is determined **at runtime** and can grow or shrink as needed.
2.  The **length (`len()`)** of a vector is the actual number of elements currently stored in the vector. The **capacity (`capacity()`)** of a vector is the total number of elements the vector can hold **without requiring a new memory allocation** on the heap. The capacity is always greater than or equal to the length. When you `push` an element and `len()` equals `capacity()`, the vector must allocate a larger block of memory, copy the elements, and update its capacity.
3.  The `[]` operator for accessing vector elements provides no runtime bounds checking in itself; if the index is outside the valid range (`0..vector.len()`), it will cause the program to terminate abruptly with a `panic!`. The `.get(index)` and `.get_mut(index)` methods, however, perform bounds checking safely. They return an `Option<T>` or `Option<&mut T>`. If the index is valid, they return `Some` containing a reference to the element; if the index is invalid, they return `None`. This allows the program to handle the potential "out of bounds" situation gracefully using `match` or `if let`, without panicking.
4.  The `.iter()` method creates an iterator that yields **immutable references (`&T`)** to the vector's elements. It takes an immutable borrow (`&self`) of the vector. The original vector remains valid and can be used or borrowed again after the iteration finishes. The `.into_iter()` method creates an iterator that **consumes** the vector itself (it takes ownership, `self`) and yields **owned values (`T`)** of its elements. After calling `.into_iter()`, the original vector variable is moved and becomes invalid, meaning you can no longer use it.
5.  When passing a `Vec<T>` to a function **by value** (`fn process_vec(v: Vec<i32>)`), the vector is **moved**, not copied. This is because `Vec<T>` manages a heap allocation and does not implement the `Copy` trait. The ownership of the vector's structure (pointer, length, capacity) is transferred to the function parameter (`v`). The original vector variable in the calling scope (e.g., `main`) becomes invalid, and any subsequent attempt to use it will result in a compile-time error, enforcing Rust's ownership rules and preventing issues like double-freeing the vector's heap buffer.

---

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025. All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech