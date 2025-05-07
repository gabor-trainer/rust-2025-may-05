**Lab 08: Understanding Ownership and Moves**

Rust's ownership system is a fundamental concept that sets it apart from many other languages. It's how Rust provides memory safety without a garbage collector. This lab will guide you through a series of exercises designed to help you understand the core mechanics of ownership and moves, especially how they differ from typical behaviors in languages like C++ and Python.

By the end of this lab, you will be able to:

*   Explain what a "move" means in Rust.
*   Identify scenarios where values are moved.
*   Understand why certain code patterns that are fine in other languages cause compile errors in Rust due to ownership.
*   Correctly use borrowing or cloning when a move is not desired.
*   Recognize types that implement the `Copy` trait.

This lab will challenge your intuition if you're coming from languages with garbage collection or conventional manual memory management. Embrace the compile errors – they are Rust's way of teaching you how to write safe code!

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`, `if/else`, loops, `Vec`).
    *   Familiarity with concepts like heap and stack memory is helpful but not strictly required to follow the rules.
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. Check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial ownership lab setup`, `feat: complete ownership exercise 1`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new ownership_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd ownership_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `ownership_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file. Use comments (`//`) to separate and label the code for each exercise. Focus on understanding *why* the initial intuitive code fails and *how* the correct Rust solution works.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`.

1.  **Simple Assignment and the Move**
    In C++ or Python, assigning one variable to another often results in both variables referencing or pointing to the *same* data. Changing one might affect the other (Python reference counting) or they might become independent copies (C++ deep copy, depending on type). Let's see what happens by default in Rust with a type that owns data, like `String`.

    *   Declare an owned `String` variable named `s1` with the value "Hello, Rust!".
    *   Declare a new variable `s2` and assign the value of `s1` to it: `let s2 = s1;`.
    *   Now, try to print `s1`: `println!("{}", s1);`.
    *   **Observe:** You should get a compile-time error.
    *   **Discuss:** Why did this fail? In Rust, for most types that own data (like `String`, which owns its heap buffer), assignment is a **move**, not a copy. Ownership of the String value (the pointer, capacity, and length) moves from `s1` to `s2`. `s1` is no longer considered initialized or valid after the move. This prevents situations where both `s1` and `s2` might try to free the same memory when they go out of scope.
    *   **Correct:** Remove the line `println!("{}", s1);` or comment it out. The assignment `let s2 = s1;` is valid; the issue is using `s1` *after* it has been moved. You can safely print `s2`.
    *   **Verification:** Run `cargo run`. The corrected code should compile and print `s2`.

2.  **Passing a Value to a Function**
    Similar to assignment, passing a value to a function in Rust also involves a move for most types.

    *   Define a simple function *outside* of `main` called `takes_ownership` that accepts one parameter `my_string` of type `String`. Inside the function, print the received string: `fn takes_ownership(my_string: String) { println!("Received string: {}", my_string); }`. The string will be dropped when the function finishes.
    *   In `main`, declare an owned `String` variable `my_data`.
    *   Call the `takes_ownership` function, passing `my_data` to it: `takes_ownership(my_data);`.
    *   After the function call, try to print `my_data`: `println!("Original data: {}", my_data);`.
    *   **Observe:** Another compile-time error!
    *   **Discuss:** Why did this fail? When you pass `my_data` (a `String`, which owns data) to `takes_ownership` by value, the ownership of the `String` is moved from the `my_data` variable in `main` to the `my_string` parameter inside the function. Once `takes_ownership` finishes, `my_string` goes out of scope, and the `String` value it owns is dropped. The `my_data` variable in `main` is no longer valid after the move into the function call. This prevents you from using `my_data` again, as the memory it pointed to has been freed.
    *   **Correct:** If you still need to use `my_data` in `main` after the function call, you cannot pass it by value. You must either:
        *   **Borrow** it: Change the function signature to `fn borrows_string(my_string: &String) { ... }` and call it with `borrows_string(&my_data);`. This is the most common approach if the function only needs to read the data. Remove the line `println!("Original data: {}", my_data);` or comment it out to avoid the error with the original function signature, or change the function call and signature to use borrowing.
        *   **Clone** it: Call the function with `takes_ownership(my_data.clone());`. This creates a separate, independent copy of the `String` which is then moved into the function, leaving the original `my_data` intact. Cloning can be expensive.
    *   **Verification:** Choose one of the "Correct" approaches (borrowing is generally preferred if possible) and run `cargo run`. The code should compile, and the string should be printed inside the function (and after the function if you used cloning).

3.  **Moves from Collections (Indexed Access)**
    In many languages, accessing an element in a list or array using an index and assigning it to a new variable often copies the element or gets a reference/pointer to it. In Rust, using `vec[index]` in a way that implies taking ownership of the element often leads to a compile error for types that don't implement `Copy`.

    *   Create a mutable `Vec<String>` containing a few `String` values.
    *   Try to get an element using index access and assign it to a new variable: `let first_element = my_vec;`.
    *   After this line, try to use `my_vec` again, for example, print its length: `println!("Vector length: {}", my_vec.len());`.
    *   **Observe:** Another compile-time error related to moving out of the vector's index.
    *   **Discuss:** Why did this fail? Rust's `Vec` (and similar collections) owns its elements contiguously in memory. It's not designed to have arbitrary "holes" created by moving individual elements out via simple index access (`[]`) if the element type requires cleanup (`Drop`, like `String`). Doing so would break the vector's internal guarantees and lead to potential memory unsafety when the vector is later dropped or manipulated.
    *   **Correct:** If you only need to *read* the element, use borrowing: `let first_element_ref = &my_vec;` or the safer `let first_element_opt_ref = my_vec.get(0);`. If you *truly* need to *take ownership* of the element out of the vector, you must use specific methods that handle the vector's state correctly:
        *   `my_vec.remove(0)`: Removes the element at index 0, shifting subsequent elements. Returns the owned element.
        *   `my_vec.swap_remove(0)`: Removes the element at index 0 by swapping it with the last element, then popping the last (original) element. Returns the owned element. Changes order but is faster than `remove`.
        *   `my_vec.into_iter().next().unwrap()` (if you want the first and consume the vector): Consumes the vector and allows iterating through owned elements.
        Choose the appropriate method based on whether you need to read (borrow) or own (move using a specific method) the element, and whether order preservation is important.
    *   **Verification:** Choose one of the "Correct" approaches (e.g., using `get()` for borrowing or `remove()` for moving/owning) and run `cargo run`. The code should compile, and you should be able to access/process the element and continue using the vector (if you used borrowing or `remove`/`swap_remove` and the indices/lengths are handled correctly).

4.  **Moves and Control Flow**
    Rust's compiler analyzes all possible execution paths to ensure ownership rules are upheld. If a value *might* be moved in one path and is then used after the paths converge, that's an error.

    *   Declare an owned `String` variable `message`.
    *   Use an `if/else` statement. Inside the `if` block, call a function `process_message` that takes a `String` by value (consumes it): `fn process_message(msg: String) { println!("Processing: {}", msg); }`. Inside the `else` block, call a *different* function `log_message` that also takes a `String` by value: `fn log_message(msg: String) { println!("Logging: {}", msg); }`.
    *   After the `if/else` block, try to print the original `message` variable: `println!("Original message after if/else: {}", message);`.
    *   **Observe:** A compile-time error related to using a moved value.
    *   **Discuss:** Why did this fail? The compiler sees that *regardless* of whether the `if` branch (calling `process_message`) or the `else` branch (calling `log_message`) is executed, the value of `message` is moved into the function parameter. Since `message` is guaranteed to have been moved *before* the `println!` after the `if/else` block, using it there is prohibited.
    *   **Correct:** If you need to use `message` after the `if/else`, the functions must not take ownership. Change both `process_message` and `log_message` to accept a shared reference (`&String`) instead: `fn process_message(msg: &String) { ... }` and `fn log_message(msg: &String) { ... }`. Update the calls in the `if/else` to pass references: `process_message(&message);` and `log_message(&message);`.
    *   **Verification:** Change the function signatures and calls to use borrowing and run `cargo run`. The code should compile, and you should be able to print the original `message` after the `if/else`.

5.  **The `Copy` Trait Exception**
    Not all types involve moves. Simple types that fit entirely on the stack and don't own any resources are `Copy`. Assignment or passing these types to functions creates a simple bitwise copy.

    *   Declare an integer variable `x` with a value (e.g., `let x = 10;`).
    *   Declare a new variable `y` and assign `x` to it: `let y = x;`.
    *   Now, try to print `x`: `println!("x is: {}", x);`.
    *   **Observe:** This time, there should be **no** compile-time error.
    *   **Discuss:** Why is this different from the `String` example in Exercise 1? The type `i32` (integer) implements the `Copy` trait. For `Copy` types, assignment is a bitwise copy, not a move. Both `x` and `y` now hold independent copies of the value `10`. Since `x` wasn't moved, it remains initialized and usable. This same behavior applies to passing `Copy` types to functions – they are copied, not moved. Other common `Copy` types include `bool`, `char`, `f32`, `f64`, and tuples/arrays containing only `Copy` types.
    *   **Further Exploration:** Define a simple struct *outside* of `main` that contains only `i32` fields (e.g., `struct Point { x: i32, y: i32 }`). Create an instance of this struct in `main`. Assign it to a new variable, and then try to use the original variable. You'll find it *fails* because structs are not `Copy` by default. Now, add `#[derive(Copy, Clone)]` above the struct definition. Try the assignment and use of the original variable again. It should now work. This demonstrates how you can opt into `Copy` for your own types if they are eligible (contain only `Copy` fields).
    *   **Verification:** Run `cargo run` with the initial `i32` example and then with the `Point` struct before and after adding `#[derive(Copy, Clone)]`. Observe the compilation results and output.

**Running the Application**

As you complete each exercise, run `cargo run` (or `cargo check` if you just want to see compile errors without running) to verify your understanding and code correctness before moving to the next one.

**Key Concepts Review**

*   **Ownership:** Every value has one owner.
*   **Drop:** When the owner goes out of scope, the value is dropped (its resources freed).
*   **Move:** By default, assigning or passing values transfers ownership. The source becomes unusable.
*   **Borrowing (`&`, `&mut`)**: Allows temporary, non-owning access to a value without moving it.
*   **`Copy` Trait:** Marks types that are copied bitwise on assignment/function calls instead of being moved.
*   **Control Flow Analysis:** The compiler tracks potential moves across different execution paths.
*   **Collection Methods:** Use specific methods (`pop`, `remove`, `swap_remove`, `into_iter`) to correctly take ownership of elements from collections like `Vec`.

**Challenges / Next Steps**

1.  **Implementing `Copy`:** Create a more complex struct that *cannot* derive `Copy` automatically (e.g., include a `String` field). Experiment with adding `#[derive(Copy, Clone)]` to see the error.
2.  **Borrowing and Lifetimes:** This lab primarily focused on moves. Research how borrowing (`&`, `&mut`) ties into Rust's concept of lifetimes, which guarantee that references are always valid.
3.  **Reference Counting (`Rc`, `Arc`)**: Investigate Rust's reference-counted pointer types (`Rc`, `Arc`) as a way to enable multiple *shared* owners for data on the heap when a single owner tree isn't sufficient (though this adds runtime overhead compared to simple ownership).

**Troubleshooting**

*   **`use of moved value: ...`**: This is the classic error indicating you tried to use a variable after its value was moved. Review the code path leading to the error and identify where the move occurred (assignment, function call, collection method that takes ownership). Fix it by borrowing (`&`, `&mut`), cloning (`.clone()`), or restructuring the code so the value isn't needed after the move.
*   **`cannot move out of ...`**: This often happens when trying to move a non-`Copy` value out of a structured type (like `Vec[index]` or a struct field accessed directly in a way implying move). Use borrowing (`&`, `&mut`), methods that return owned values correctly (`Vec::remove`), or wrap the value in `Option` and use `Option::take()`.

**Conclusion**

You have now gained practical experience with Rust's ownership and move semantics. Understanding when values are moved and how to use borrowing and the `Copy` trait are foundational to writing safe and efficient Rust code. These concepts can feel strict initially, but they empower the compiler to prevent common errors that often lead to bugs and security vulnerabilities in other languages.

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete ownership lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Check Questions (To Test Understanding)**

1.  When does an assignment like `let b = a;` perform a move in Rust, and when does it perform a copy?
2.  If a function `fn process(data: String)` is called with `process(my_string);` where `my_string: String`, can `my_string` be used again after the function call returns? Why or why not?
3.  Why does attempting `let element = my_vec;` cause a compile error if `my_vec: Vec<MyStruct>` and `MyStruct` does NOT implement `Copy`?
4.  If a variable `x` is declared before an `if/else` block, and *both* the `if` and `else` branches call a function that takes `x` by value, can `x` be used after the `if/else` block? Explain the compiler's reasoning.
5.  Name at least two types in Rust's standard library that you expect to implement the `Copy` trait, and explain why they likely do.

**Detailed Answers to Check Questions**

1.  An assignment `let b = a;` performs a **move** if the type of `a` does **not** implement the `Copy` trait. Ownership of the value is transferred from `a` to `b`, and `a` becomes unusable. It performs a **copy** if the type of `a` **does** implement the `Copy` trait. The value is bitwise-copied, and both `a` and `b` remain usable with their own independent copies of the data.
2.  No, `my_string` **cannot** be used again after calling `process(my_string);`. Because `data: String` in the function signature, the `String` value owned by `my_string` is moved into the `data` parameter when the function is called. When the `process` function finishes, the `data` variable goes out of scope, and the `String` value it owns is dropped. The original `my_string` variable is then invalid.
3.  It causes a compile error because using `my_vec` in a `let` assignment context when the type (`MyStruct`) is not `Copy` implies a **move** of the `MyStruct` value out of the vector's internal storage at that specific index. `Vec` is designed to own a contiguous block of memory for its elements. Moving an element out like this would leave an invalid or uninitialized state ("hole") at that position, which violates the `Vec`'s guarantees and cannot be safely handled when the `Vec` is later dropped or elements are accessed/shifted. `Vec` does not support creating arbitrary holes in its contiguous memory via indexing for non-`Copy` types.
4.  No, `x` **cannot** be used after the `if/else` block. Rust's compiler performs control flow analysis. It determines that regardless of whether the `if` branch or the `else` branch is executed, the value owned by `x` is moved into the function parameter in *both* cases. Since *all* possible paths leading out of the `if/else` result in `x` being moved, the compiler knows `x` is guaranteed to be in an unusable state after the block finishes and prevents any subsequent uses.
5.  Examples of types that likely implement `Copy` include:
    *   `i32` (or any other integer type like `u64`, `isize`): They are simple fixed-size values that fit entirely on the stack and do not own any external resources. Copying them is just copying bits; no special cleanup is needed.
    *   `bool`: Represents a simple true/false value, also fits entirely on the stack and owns no resources.
    *   `char`: Represents a Unicode scalar value, fits entirely on the stack and owns no resources.
    *   `f64` (or any other floating-point type): Simple fixed-size numeric values.
    *   A tuple like `(i32, bool)`: If all elements within a tuple (or fixed-size array `[T; N]`) implement `Copy`, the tuple/array itself implements `Copy`.
*   
---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*