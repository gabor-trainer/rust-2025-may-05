**Lab 9: Shared Ownership with `Rc<T>`**

Rust's ownership system typically enforces a single owner for each value. However, scenarios arise where multiple parts of your program need to share access to the same piece of data on the heap. This is where `Rc<T>` (Reference Counting) comes in. This lab will introduce you to `Rc<T>` as a way to enable multiple shared owners in a single-threaded context, allowing you to build more complex data structures and sharing patterns than basic ownership permits.

By the end of this lab, you will be able to:

*   Create `Rc<T>` instances to hold heap-allocated data.
*   Use `Rc::clone()` to create new shared pointers (handles) to the same data.
*   Understand how `Rc<T>` tracks the number of active references.
*   Observe when the data managed by `Rc<T>` is dropped.
*   Recognize the limitation of `Rc<T>` regarding mutable access.

This lab focuses specifically on `Rc<T>` for single-threaded scenarios. It builds upon your understanding of basic ownership and borrowing.

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`, structs).
    *   Familiarity with `Vec` (vectors).
    *   A solid understanding of Rust's basic ownership and borrowing rules (moves, shared references `&`, mutable references `&mut`).
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. Check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial rc lab setup`, `feat: complete rc exercise 1`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new rc_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd rc_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `rc_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file. Use comments (`//`) to separate and label the code for each exercise.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`.

1.  **Basic `Rc::new` and `Rc::clone`**
    Create a value and put it inside an `Rc`. Then, create another `Rc` handle that points to the same data. Observe the reference count.

    *   Import the necessary type: `use std::rc::Rc;`.
    *   Create an `Rc` instance holding a simple value, like an integer. Use `Rc::new()`.
    *   Create a second `Rc` handle pointing to the *same* integer. Use `Rc::clone()` on the first handle. `Rc::clone(&handle)` is the standard way to get a new handle and increment the count.
    *   Print the "strong" reference count for both handles using `Rc::strong_count(&handle)`.
    *   **Verification:** Run `cargo run`. You should see the count increment after cloning.

2.  **Shared Data Access**
    Demonstrate that multiple `Rc` handles refer to the exact same underlying data instance on the heap.

    *   Create an `Rc` holding a simple `String`.
    *   Create a second `Rc` handle to the same string using `clone()`.
    *   Access the string value through both handles (e.g., print them).
    *   **Hints:** You can treat an `Rc<String>` very much like a `&String` for immutable access.
    *   **Verification:** Run `cargo run`. You should see the same string value printed via two different variables holding `Rc` handles.

3.  **`Rc` and Functions**
    Explore how passing `Rc` handles to functions affects ownership and the reference count.

    *   Define a function *outside* of `main`, say `print_rc_count`, that takes an `Rc<String>` as an argument **by value**. Inside this function, print the value and the strong count of the `Rc` handle it received.
    *   In `main`, create an `Rc` holding a `String`. Create a clone of this handle.
    *   Print the initial strong count in `main`.
    *   Call `print_rc_count` with one of the `Rc` handles.
    *   After the function call, print the strong count again in `main`.
    *   **Discuss:** What happened to the count when the handle was passed to the function? What happened to the count after the function returned? When you pass an `Rc` handle by value, ownership of the *handle* is moved into the function parameter, incrementing the count upon entry and decrementing it upon exit (when the parameter goes out of scope).
    *   **Modify:** Change the function `print_rc_count` to accept a **shared reference** to an `Rc<String>`: `fn print_rc_count_ref(handle: &Rc<String>)`. Update the call site in `main` to pass `&handle`. Observe the counts.
    *   **Discuss:** How does passing a reference to the handle differ in terms of count changes? Passing a reference does not move the handle itself, so the reference count is unaffected by the function call. This is similar to how borrowing a `Vec` doesn't change its ownership.
    *   **Verification:** Run `cargo run` and observe the strong counts printed before, during (inside the function), and after the function calls for both by-value and by-reference passing.

4.  **Dropping `Rc` Handles**
    Understand when the data inside the `Rc` is actually dropped.

    *   Create an `Rc` instance holding a value (e.g., a `String`).
    *   Create several clones of this `Rc` handle. Print the strong count.
    *   Explicitly `drop()` one of the cloned handles. Print the count again.
    *   Use a `{}` block to create a new scope. Inside the block, create another clone of the `Rc` handle. Print the count inside the block.
    *   After the block ends, print the count again.
    *   **Discuss:** When did the strong count decrease? When do you expect the actual data (the `String`) to be dropped? The data is only dropped when the *last* strong reference count is decreased (either by an `Rc` handle going out of scope or being explicitly dropped), reaching zero.
    *   **Verification:** Run `cargo run`. The counts should decrease as handles are dropped or go out of scope. You won't see explicit "dropped" messages for simple types, but you can infer when the last handle is gone.

5.  **Attempting Mutable Access**
    Confirm that `Rc` itself does not provide mutable access to the inner data.

    *   Create an `Rc` instance holding a mutable value type, like a `Vec<i32>`.
    *   Try to modify the vector through the `Rc` handle. For example, attempt to `push()` a new element or modify an existing element using indexing: `my_rc_vec.push(4);` or `my_rc_vec = 10;`.
    *   **Observe:** You should get a compile-time error related to mutable borrowing.
    *   **Discuss:** Why does this fail? `Rc` provides shared access (`&T`). Modifying the `Vec` requires mutable access (`&mut Vec<i32>`). The compiler prevents you from getting mutable access through a shared `Rc` handle because that would violate the borrowing rules if multiple handles exist, potentially leading to data races or other unsafety in a single-threaded context. This demonstrates that `Rc` alone is for sharing *immutable* data.
    *   **Hint:** To achieve shared mutability in a single-threaded context, you need to combine `Rc` with an interior mutability type like `RefCell`. However, you do not need to implement `RefCell` in this exercise; just understand why it's necessary.
    *   **Verification:** Run `cargo check` (or `cargo run`). The compiler error is the expected outcome.

6.  **Using `Rc` with a Custom Struct**
    Apply `Rc` to share instances of your own defined types.

    *   Define a simple struct, e.g., `Config { setting: String, value: i32 }`, outside of `main`.
    *   In `main`, create an instance of this struct and wrap it in an `Rc`.
    *   Create one or two clones of the `Rc<Config>` handle.
    *   Define a function `print_config` that takes a shared reference to an `Rc<Config>` (`&Rc<Config>`). Inside the function, access and print the fields of the `Config` struct through the `Rc` handle.
    *   Call `print_config` from `main` using one of your `Rc` handles.
    *   **Hints:** Access fields through the `Rc` handle using dot notation, e.g., `handle.setting`.
    *   **Verification:** Run `cargo run`. The function should successfully print the config details, demonstrating safe, shared, immutable access to the struct instance on the heap via `Rc`.

**Running the Application**

As you complete each exercise, run `cargo run` (or `cargo check` if the step is expected to cause a compile error) to verify your understanding and code correctness before moving to the next one.

**Key Concepts Review**

*   `Rc<T>`: Provides shared ownership in a single-threaded context. Allocates `T` on the heap.
*   `Rc::new(value)`: Creates the first `Rc` handle, placing the value on the heap with a strong count of 1.
*   `Rc::clone(&handle)`: Creates a new `Rc` handle pointing to the *same* heap data and increments the strong count. This is a cheap operation (copying the pointer and incrementing an integer).
*   `Rc::strong_count(&handle)`: Returns the current number of active `Rc` handles pointing to the data.
*   Dropping `Rc` Handles: When an `Rc` handle goes out of scope or is explicitly `drop`ped, the strong count is decremented.
*   Data Dropping: The data on the heap managed by `Rc` is dropped *only* when the strong count reaches zero.
*   Immutability: `Rc<T>` only provides shared (`&T`) access to the inner data. It *does not* allow mutable access (`&mut T`) directly.

**Challenges / Next Steps**

1.  **Shared Mutability with `RefCell`:** Extend Exercise 5. Wrap the `Vec<i32>` in a `RefCell` *before* putting it in the `Rc` (`Rc<RefCell<Vec<i32>>>`). Use `.borrow_mut()` on the `RefCell` to modify the vector. Understand the runtime panic if borrow rules are violated.
2.  **Weak References (`Rc::downgrade`):** Research `Rc::downgrade()` and `Weak<T>`. How can `Weak` references break reference cycles that would otherwise cause memory leaks with only `Rc`?
3.  **Thread-Safe Sharing (`Arc<T>`)**: Investigate `Arc<T>`. When would you use `Arc` instead of `Rc`? (Hint: When sharing data between multiple threads). How would you achieve thread-safe mutability with `Arc`? (Hint: `Arc<Mutex<T>>`).

**Troubleshooting**

*   **`use of moved value: ...`**: This can happen if you try to use an `Rc` handle after it was passed *by value* to a function without cloning it first. Remember that passing an `Rc` by value moves the handle.
*   **`cannot borrow as mutable`**: This is the expected error from Exercise 5 when trying to modify data directly through an `Rc` handle. Remember `Rc` is for shared, *immutable* access. You need interior mutability (`RefCell`) for shared mutability.
*   **Runtime Panic (related to `RefCell::borrow_mut` if you attempt Challenge 1):** If you implement Challenge 1 and your program panics with a message like "already borrowed: BorrowMutError", it means you attempted to get a mutable borrow from a `RefCell` while another borrow (shared or mutable) was already active. This is `RefCell` enforcing Rust's borrowing rules at runtime. Ensure only one mutable or multiple shared borrows are active at any given moment.

**Conclusion**

You have successfully explored the basics of `Rc<T>` in Rust, understanding how it enables shared ownership, tracks references, and ensures data is dropped appropriately in single-threaded applications. While `Rc` is powerful for sharing, remember its limitation regarding mutability, which points towards the need for tools like `RefCell`.

**Final Solution Code (src/main.rs)**

Here is the complete `main` function containing the solutions for all 6 exercises. Review it to compare with your own implementations. Note that the function `print_rc_count` and `print_rc_count_ref` for Exercise 3 are defined outside of `main`.

```rust
// src/main.rs

use std::rc::Rc;
use std::mem; // Needed for explicit drop in Exercise 4

fn main() {
    println!("--- Rust Rc<T> Lab Solutions ---");

    // 1. Exercise: Basic Rc::new and Rc::clone
    println!("\n--- Exercise 1: Basic Rc::new and Rc::clone ---");
    let value = 10; // The value to be shared
    let rc_handle1 = Rc::new(value); // Create the first Rc handle, count = 1
    println!("Initial strong count after Rc::new(): {}", Rc::strong_count(&rc_handle1));

    let rc_handle2 = Rc::clone(&rc_handle1); // Create a clone (new handle), count = 2
    println!("Strong count after Rc::clone(): {}", Rc::strong_count(&rc_handle1));
    // Note: You can use Rc::strong_count(&rc_handle2) as they point to the same counter


    // 2. Exercise: Shared Data Access
    println!("\n--- Exercise 2: Shared Data Access ---");
    let shared_string = Rc::new(String::from("This string is shared"));
    let shared_string_clone1 = Rc::clone(&shared_string);
    let shared_string_clone2 = shared_string.clone(); // .clone() is often equivalent to Rc::clone(&handle)

    println!("Access via shared_string: {}", shared_string);
    println!("Access via shared_string_clone1: {}", shared_string_clone1);
    println!("Access via shared_string_clone2: {}", shared_string_clone2);
    println!("Current strong count: {}", Rc::strong_count(&shared_string));


    // 3. Exercise: Rc and Functions
    println!("\n--- Exercise 3: Rc and Functions ---");
    // Function 'print_rc_count' and 'print_rc_count_ref' defined outside main

    let func_rc = Rc::new(String::from("String for function demo"));
    println!("Count before passing Rc by value: {}", Rc::strong_count(&func_rc));
    print_rc_count(Rc::clone(&func_rc)); // Pass a clone by value, so func_rc is still valid
    // If you pass func_rc directly: print_rc_count(func_rc); then func_rc is moved and unusable here.
    println!("Count after passing Rc by value (original handle): {}", Rc::strong_count(&func_rc));

    println!("\nCount before passing Rc by reference: {}", Rc::strong_count(&func_rc));
    print_rc_count_ref(&func_rc); // Pass a reference to the handle
    println!("Count after passing Rc by reference: {}", Rc::strong_count(&func_rc));


    // 4. Exercise: Dropping Rc Handles
    println!("\n--- Exercise 4: Dropping Rc Handles ---");
    let data_to_drop = Rc::new(String::from("Data that will be dropped"));
    let d1 = Rc::clone(&data_to_drop);
    let d2 = Rc::clone(&data_to_drop);
    println!("Initial count for dropping demo: {}", Rc::strong_count(&data_to_drop)); // Count is 3 (data_to_drop, d1, d2)

    drop(d1); // Explicitly drop one handle
    println!("Count after dropping d1: {}", Rc::strong_count(&data_to_drop)); // Count is 2 (data_to_drop, d2)

    { // New scope
        let d3 = Rc::clone(&data_to_drop);
        println!("Count inside scope (d3 exists): {}", Rc::strong_count(&data_to_drop)); // Count is 3 (data_to_drop, d2, d3)
    } // d3 goes out of scope here, count decreases

    println!("Count after scope ends (d3 dropped): {}", Rc::strong_count(&data_to_drop)); // Count is 2 (data_to_drop, d2)

    // When data_to_drop and d2 go out of scope at the end of main, the count will drop to 0,
    // and the String "Data that will be dropped" will be dropped.


    // 5. Exercise: Attempting Mutable Access
    println!("\n--- Exercise 5: Attempting Mutable Access ---");
    let rc_vec_attempt = Rc::new(vec![10, 20, 30]);
    // This will NOT compile:
    // rc_vec_attempt.push(40); // error: cannot borrow `*rc_vec_attempt` as mutable
    // rc_vec_attempt[0] = 100; // error: cannot borrow `*rc_vec_attempt` as mutable
    println!("Attempting mutation with Rc alone causes compile error (as expected).");
    println!("Rc only provides shared (immutable) access: {:?}", rc_vec_attempt);


    // 6. Exercise: Using Rc with a Custom Struct
    println!("\n--- Exercise 6: Using Rc with a Custom Struct ---");
    // Struct 'Config' defined outside main

    let my_config = Rc::new(Config {
        setting: String::from("AppVersion"),
        value: 1,
    });

    let config_clone1 = Rc::clone(&my_config);
    let config_clone2 = Rc::clone(&my_config);

    println!("Initial config count: {}", Rc::strong_count(&my_config));

    // Pass a reference to an Rc handle to the function
    print_config(&my_config);
    print_config(&config_clone1); // Can pass any handle

    println!("Config count after function calls: {}", Rc::strong_count(&my_config));


    println!("\n--- Lab Complete ---");
}

// Function definitions for Exercise 3 (placed outside main)
fn print_rc_count(my_string: Rc<String>) {
    // 'my_string' now owns a handle, the count incremented upon entry
    println!("Inside print_rc_count: Received '{}'", my_string);
    println!("Inside print_rc_count: Strong count is {}", Rc::strong_count(&my_string));
    // 'my_string' handle goes out of scope here, count will decrement
}

fn print_rc_count_ref(handle: &Rc<String>) {
    // 'handle' is a reference to the Rc handle, no change in ownership or count upon entry/exit
     println!("Inside print_rc_count_ref: Received reference to '{}'", handle);
    println!("Inside print_rc_count_ref: Strong count is {}", Rc::strong_count(handle));
}

// Struct definition for Exercise 6 (placed outside main)
#[derive(Debug)] // Derive Debug for easy printing
struct Config {
    setting: String,
    value: i32,
}

// Function definition for Exercise 6 (placed outside main)
fn print_config(handle: &Rc<Config>) {
    // We have a reference to an Rc handle
    println!("Inside print_config:");
    println!("  Setting: {}", handle.setting); // Access fields through the Rc handle
    println!("  Value: {}", handle.value);
    println!("  Current Rc count for this handle: {}", Rc::strong_count(handle));
}

```

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete rc lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Check Questions (To Test Understanding)**

1.  What is the primary purpose of `Rc<T>` in Rust?
2.  How do you create a new handle to an existing `Rc<T>` instance, and what happens to the reference count when you do this?
3.  When is the data managed by an `Rc<T>` actually dropped?
4.  If you have a variable `my_rc: Rc<Vec<i32>>` and try to call `my_rc.push(5);`, why does this fail to compile?
5.  In the context of passing an `Rc<T>` to a function, explain the difference in effect on the reference count between passing `my_rc` (by value) and passing `&my_rc` (by reference).

**Detailed Answers to Check Questions**

1.  The primary purpose of `Rc<T>` is to enable **multiple shared owners** of data allocated on the heap within a **single-threaded** application. It allows different parts of the program to point to the same data and ensures the data remains valid as long as at least one owner exists.
2.  You create a new handle to an existing `Rc<T>` instance by calling the `.clone()` method on an existing handle (e.g., `let new_handle = existing_handle.clone();` or `let new_handle = Rc::clone(&existing_handle);`). When you do this, the **strong reference count** associated with the heap-allocated data is **incremented by one**.
3.  The data managed by an `Rc<T>` is actually dropped (its destructor is called, and its memory is freed) **only when the strong reference count reaches zero**. This happens when the last `Rc<T>` handle pointing to that data goes out of scope or is explicitly `drop`ped.
4.  This fails to compile because `Rc<T>` only provides **shared (immutable)** access to the inner data `T`. The `push()` method on `Vec<i32>` requires **exclusive (mutable)** access (`&mut self`). Rust's compiler prevents you from obtaining mutable access through a shared `Rc` handle because it cannot guarantee that there isn't another `Rc` handle existing simultaneously, which would violate the rule of "either one mutable borrow or many shared borrows".
5.  When passing `my_rc` by value (`fn func(handle: Rc<T>)`), ownership of the `Rc` handle is **moved** into the function's parameter. This causes the strong reference count to be **incremented** upon function entry (as a new handle comes into existence as the parameter) and **decremented** upon function exit (as the parameter goes out of scope). When passing `&my_rc` by reference (`fn func(handle_ref: &Rc<T>)`), a shared reference to the `Rc` handle itself is passed. This does **not** involve transferring ownership of the handle or creating a new handle, so the strong reference count of the data within the `Rc` remains **unchanged** by the function call.

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*