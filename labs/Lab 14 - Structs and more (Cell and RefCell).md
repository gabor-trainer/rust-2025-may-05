**Lab 14: Structs and more... (Cell and RefCell)**

Structs are fundamental building blocks in Rust, allowing you to group related data and define behavior associated with that data. This lab explores the different kinds of structs Rust offers, how to add methods and associated functions to them, make them generic, derive common traits for convenience, and manage mutability within immutable contexts using interior mutability types like `Cell` and `RefCell`.

By the end of this lab, you will be able to:

*   Define and instantiate named-field, tuple-like, and unit-like structs.
*   Access struct fields/elements.
*   Define methods (`&self`, `&mut self`, `self`) and type-associated functions (`::new()`) using `impl` blocks.
*   Add associated constants to structs.
*   Define generic structs with type parameters.
*   (Briefly) understand how lifetime parameters apply to structs with references.
*   (Briefly) understand how const parameters apply to structs with arrays.
*   Use `#[derive]` to automatically implement common traits like `Debug`, `Copy`, and `Clone`.
*   Employ `Cell` and `RefCell` for interior mutability in single-threaded scenarios.

This lab provides a comprehensive overview of Rust's struct system, equipping you with the tools to model data and behavior effectively.

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`, enums).
    *   Understanding of ownership, borrowing (`&`, `&mut`), and moves.
    *   Familiarity with `Vec` and primitive types.
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. Check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial structs lab setup`, `feat: complete named-field struct exercise`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new structs_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd structs_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `structs_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file. You will define structs and `impl` blocks *outside* of the `main` function.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file, unless otherwise specified. Use comments (`//`) to separate and label the code for each exercise.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`, unless otherwise specified.

1.  **Named-Field Structs: Definition and Instantiation**
    Define a struct where each data component has a name.

    *   **Concept:** Named-field structs group values with descriptive names.
    *   Define a struct `UserProfile` *outside* of `main` with fields `username` (`String`), `active` (`bool`), and `login_count` (`u64`).
    *   In `main`, create an instance of `UserProfile`, providing values for each field.
    *   Access and print the `username` and `login_count` fields using the `.` operator.
    *   **Hint:** Struct definitions use `struct Name { field: Type, ... }`. Instantiation uses `Name { field: value, ... }`.
    *   **Verification:** Run `cargo run`. The program should compile and print the accessed fields.

2.  **Named-Field Structs: Field Init Shorthand and Struct Update Syntax**
    Explore shortcuts for initializing fields and creating a new struct based on an existing one.

    *   **Concept:** Convenient syntax for struct instantiation when variable names match field names, and for creating new structs based on existing ones.
    *   Define a function `build_profile` *outside* of `main` that takes `username` (`String`) and `login_count` (`u64`) as parameters. Inside this function, create and return a `UserProfile` struct. Use the field init shorthand where possible. Set `active` to `true` by default.
    *   In `main`, call `build_profile` to create an initial `UserProfile`.
    *   Create a *new* `UserProfile` instance based on the first one, but update the `login_count` to a higher value and change `active` to `false`. Use the struct update syntax (`..`). Remember `String` is not `Copy`, so you might need to handle the `username` field carefully if you want to *move* or *clone* it. For this exercise, let's update `login_count` and `active`, and the `username` will be moved if not explicitly handled (leading to potential errors later if the original is used). To keep it simple, just update `login_count` and `active` and assume you don't need the original after the update for now.
    *   Print the `login_count` and `active` fields of the *new* profile instance.
    *   **Hint:** `Struct { field: value, .. existing_struct }`.
    *   **Verification:** Run `cargo run`. The printed fields should reflect the updated values from the new struct instance.

3.  **Tuple-Like Structs**
    Define a struct where components are accessed by index, like a tuple.

    *   **Concept:** Tuple-like structs provide a name to a tuple type, useful for newtypes or simple groupings where field names aren't necessary.
    *   Define a tuple-like struct `Color(u8, u8, u8)` *outside* of `main` to represent RGB values.
    *   In `main`, create an instance of `Color`, providing values for red, green, and blue.
    *   Access and print the individual RGB components using `.0`, `.1`, and `.2`.
    *   **Hint:** Struct definition is `struct Name(Type, ...);`. Instantiation is `Name(value, ...);`. Access is `instance.index`.
    *   **Verification:** Run `cargo run`. The code should compile and print the RGB values.

4.  **Unit-Like Structs**
    Define a struct with no fields.

    *   **Concept:** Unit-like structs are useful as markers or when implementing traits where no data needs to be stored.
    *   Define a unit-like struct `ProcessingComplete` *outside* of `main`.
    *   In `main`, create an instance of `ProcessingComplete`.
    *   You can simply print its type or just declare it. Observe that it takes no memory.
    *   **Hint:** Struct definition is `struct Name;`. Instantiation is `Name;`.
    *   **Verification:** Run `cargo run`. The code should compile. You won't see much output from the struct itself, but its declaration and instantiation should not cause errors.

5.  **Defining Methods (`&self`, `&mut self`)**
    Add behavior to a struct using `impl` blocks and methods that borrow the struct instance.

    *   **Concept:** Associate functions with a struct type that operate on a specific instance using `self`.
    *   Use the `UserProfile` struct from previous exercises.
    *   Define an `impl UserProfile { ... }` block *outside* of `main`.
    *   Inside the `impl` block, define a method `is_active(&self) -> bool` that returns the value of the `active` field. This uses a shared borrow (`&self`).
    *   Define a method `increment_login_count(&mut self)` that increments the `login_count` field by 1. This uses a mutable borrow (`&mut self`).
    *   In `main`, create a *mutable* `UserProfile` instance.
    *   Call the `is_active()` method and print the result.
    *   Call the `increment_login_count()` method.
    *   Access and print the `login_count` field after calling the mutable method to see the change.
    *   **Hint:** Methods are functions inside an `impl` block where the first parameter is `self`, `&self`, or `&mut self`.
    *   **Crucial Lines:**
        ```rust
        impl UserProfile {
            fn is_active(&self) -> bool {
                // ... access self.active ...
            }
            fn increment_login_count(&mut self) {
                // ... modify self.login_count ...
            }
        }
        // In main:
        let mut user = UserProfile { /* ... */ };
        user.increment_login_count(); // Call the method
        ```
    *   **Verification:** Run `cargo run`. The `active` status should be printed, and the login count should show the incremented value.

6.  **Defining Methods (`self`)**
    Define a method that takes ownership of the struct instance.

    *   **Concept:** Methods can consume the struct instance they are called on.
    *   Use the `UserProfile` struct.
    *   Define a method `into_username(self) -> String` *inside the `impl UserProfile { ... }` block* that takes ownership of `self` and returns the `username` field.
    *   In `main`, create a `UserProfile` instance.
    *   Call the `into_username()` method and assign the result to a new `String` variable.
    *   After calling `into_username()`, try to print the original `UserProfile` variable (e.g., `println!("{:?}", user);`).
    *   **Observe:** This should cause a compile error.
    *   **Discuss:** Why did this fail? The `into_username` method took `self` by value, which moved the ownership of the `UserProfile` instance into the method. After the method returned the `username`, the `UserProfile` instance inside the method was dropped, leaving the original variable in `main` uninitialized.
    *   **Verification:** Run `cargo check`. The compile error is the expected outcome for attempting to use the variable after the move.

7.  **Type-Associated Functions (`::new()`)**
    Define functions associated with the struct type itself, not a specific instance.

    *   **Concept:** Functions within an `impl` block that do not take `self` as the first argument are called type-associated functions and are often used as constructors.
    *   Use the `UserProfile` struct.
    *   Inside the `impl UserProfile { ... }` block, define a public type-associated function `new(username: String) -> UserProfile` that creates a new `UserProfile` with the given username, `active` set to `true`, and `login_count` set to `0`.
    *   In `main`, call this function using the double colon syntax (`::`): `UserProfile::new("Alice".to_string())`.
    *   Assign the result to a variable and print its fields.
    *   **Hint:** Associated functions are called using `StructName::function_name(...)`. Constructor functions are conventionally named `new`.
    *   **Verification:** Run `cargo run`. The program should compile and print the fields of the struct created by `UserProfile::new()`.

8.  **Associated Consts**
    Define constant values associated with the struct type itself.

    *   **Concept:** Constants defined within an `impl` block, accessed via the type name.
    *   Use the `UserProfile` struct.
    *   Inside the `impl UserProfile { ... }` block, define an associated constant `DEFAULT_USERNAME` of type `&'static str` with a default value (e.g., `"anonymous"`).
    *   In `main`, access and print the value of `UserProfile::DEFAULT_USERNAME`.
    *   **Hint:** Associated constants are defined using `const NAME: Type = value;` inside an `impl` block and accessed via `StructName::NAME`.
    *   **Verification:** Run `cargo run`. The program should compile and print the value of the associated constant.

9.  **Generic Structs with Type Parameters**
    Make a struct generic over one or more types.

    *   **Concept:** Structs can be defined with type parameters using `<T, U, ...>` to work with different types.
    *   Define a generic struct `DataContainer<T>` *outside* of `main` with one field `data` of type `T`.
    *   In `main`, create instances of `DataContainer` holding different types, like `DataContainer<i32>` with an `i32` and `DataContainer<String>` with a `String`.
    *   Access and print the `data` field of both instances.
    *   **Hint:** Use angle brackets `<T>` after the struct name. You don't always need to explicitly write the type parameter during instantiation if Rust can infer it.
    *   **Verification:** Run `cargo run`. The code should compile and print the data from the generic structs.

10. **Deriving Common Traits (`Debug`, `Copy`, `Clone`)**
    Automatically implement standard traits using the `#[derive]` attribute.

    *   **Concept:** Rust can generate implementations for common traits for your structs if their fields meet the requirements.
    *   Define a simple struct `Point { x: f64, y: f64 }` *outside* of `main`.
    *   Add `#[derive(Debug, Copy, Clone)]` above the struct definition.
    *   In `main`, create a `Point` instance.
    *   Print the instance using the debug formatter (`println!("{:?}", point);`). This requires the `Debug` trait.
    *   Create a new variable and assign the `Point` instance to it (`let another_point = my_point;`). Print the original `my_point` again. This requires the `Copy` trait.
    *   **Discuss:** If you remove `#[derive(Copy, Clone)]`, the assignment `let another_point = my_point;` would move ownership, and the second `println!("{:?}", my_point);` would cause a compile error.
    *   **Verification:** Run `cargo run`. The program should compile and print the point using debug formatting, and print the original point twice without errors due to `Copy`.

11. **Interior Mutability: `Cell` (for `Copy` types)**
    Use `Cell` to allow mutable access to a `Copy` type field within a struct that you only have a shared reference to.

    *   **Concept:** `Cell<T>` allows getting and setting the inner value `T` even with `&self` if `T` is `Copy`.
    *   Define a struct `StatusTracker` *outside* of `main` with one field: `count` of type `Cell<u32>`. You'll need `use std::cell::Cell;`.
    *   Define an `impl StatusTracker { ... }` block.
    *   Inside the `impl` block, define a method `increment_count(&self)` that increments the `count` field using `.get()` and `.set()`. Note it takes `&self`.
    *   Define a method `get_count(&self) -> u32` that returns the value of the `count` field using `.get()`.
    *   In `main`, create an instance of `StatusTracker`.
    *   Call `increment_count()` several times using a shared reference (or directly on the variable if not borrowed elsewhere).
    *   Call `get_count()` and print the result.
    *   **Verification:** Run `cargo run`. The printed count should reflect the increments made via the `increment_count` method, even though it took `&self`.

12. **Interior Mutability: `RefCell` (for non-`Copy` types)**
    Use `RefCell` to allow mutable access to a non-`Copy` type field within a struct that you only have a shared reference to.

    *   **Concept:** `RefCell<T>` allows getting `Ref<T>` (`&T` equivalent) and `RefMut<T>` (`&mut T` equivalent) at runtime using `.borrow()` and `.borrow_mut()`, even with `&self`. It enforces borrowing rules at runtime.
    *   Define a struct `LogBuffer` *outside* of `main` with one field: `messages` of type `RefCell<Vec<String>>`. You'll need `use std::cell::RefCell;`.
    *   Define an `impl LogBuffer { ... }` block.
    *   Inside the `impl` block, define a method `add_message(&self, msg: String)` that adds a message to the `messages` vector using `.borrow_mut()`. Note it takes `&self`.
    *   Define a method `print_messages(&self)` that iterates over and prints the messages using `.borrow()`.
    *   In `main`, create an instance of `LogBuffer`.
    *   Call `add_message()` several times.
    *   Call `print_messages()`.
    *   **Verification:** Run `cargo run`. The added messages should be printed, demonstrating mutable access to the `Vec<String>` field via `RefCell` methods while only holding a shared reference to the `LogBuffer`.

**Running the Application**

As you complete each exercise, run `cargo run` (or `cargo check` if the step is expected to cause a compile error) to verify your understanding and code correctness before moving to the next one.

**Key Concepts Review**

*   **Structs:** Named-field, tuple-like, and unit-like types for grouping data.
*   **`impl` Blocks:** Used to define associated functions (methods and type-associated functions) and associated constants for a type.
*   **Methods:** Associated functions with a `self` receiver (`self`, `&self`, `&mut self`).
*   **Type-Associated Functions:** Associated functions without a `self` receiver, called using `StructName::function()`. Conventionally `new` is used for constructors.
*   **Associated Consts:** Constants defined within an `impl` block, accessed via `StructName::CONST`.
*   **Generics on Structs**: Using `<T, U, ...>` after the struct name to create a type template.
*   **`#[derive]`**: Attribute to automatically generate implementations for standard traits.
*   **`Debug`**: Trait for debug printing (`{:?}`).
*   **`Copy`**: Trait indicating a type can be copied bitwise.
*   **`Clone`**: Trait indicating a type can be cloned explicitly (`.clone()`).
*   **Interior Mutability (`std::cell`)**: Allows mutable access to data through a shared reference (`&`).
*   **`Cell<T>`**: For `Copy` types; allows getting and setting the inner value with `&self`.
*   **`RefCell<T>`**: For any type; allows getting `Ref<T>` (`&`) and `RefMut<T>` (`&mut`) at runtime with `&self`, panicking if borrow rules are violated.

**Challenges / Next Steps**

1.  **Newtype Pattern with Methods:** Combine the tuple-like struct concept with methods by creating a newtype struct and implementing methods on it (e.g., a `Meters(f64)` struct with an `add` method).
2.  **Implementing Traits Manually:** Choose a simple trait (like `Display` or `PartialEq`) and manually implement it for one of your structs instead of using `#[derive]`.
3.  **Interior Mutability in Multi-threaded Contexts:** Research `std::sync::Mutex` and `std::sync::RwLock`. How do they provide thread-safe interior mutability compared to `Cell` and `RefCell`?

**Troubleshooting**

*   **Missing Field / Extra Field in Struct Literal**: When instantiating a named-field struct, ensure you provide exactly one value for each field name defined in the struct.
*   **Incorrect Number of Elements in Tuple-Like Struct**: When instantiating a tuple-like struct, provide the correct number of values in the parentheses matching the definition.
*   **Missing `mut`**: Remember to declare a variable with `mut` if you need to call methods that take `&mut self`.
*   **Attempting to Use Variable After `self` Method**: If you define a method that takes `self` by value, the original variable holding the struct instance will be moved and cannot be used after the method call.
*   **Borrowing Errors with Interior Mutability**: If you use `RefCell::borrow_mut()` and get a runtime panic, you have violated Rust's borrowing rules at that moment (e.g., tried to get a mutable borrow while another borrow was active). Ensure only one mutable or many shared borrows exist concurrently.

**Conclusion**

You have successfully explored the various forms of structs in Rust, learned how to associate behavior with them using `impl` blocks and methods, and tackled the concept of interior mutability. These are foundational concepts for building data structures and modeling the world in your Rust programs.

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete structs lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Check Questions (To Test Understanding)**

1.  What are the three kinds of struct types in Rust, and how do you access their components?
2.  Explain the difference between a method and a type-associated function.
3.  When would you typically use `&self`, `&mut self`, or `self` as the first parameter of a method?
4.  What does the `#[derive(Debug, Copy, Clone)]` attribute do for a struct?
5.  Explain the difference between `std::cell::Cell<T>` and `std::cell::RefCell<T>` and when you would use one over the other for interior mutability.

**Detailed Answers to Check Questions**

1.  The three kinds of struct types are:
    *   **Named-field structs**: Components are accessed by name using the `.` operator (e.g., `instance.field_name`).
    *   **Tuple-like structs**: Components are accessed by their zero-based index using the `.` operator (e.g., `instance.0`, `instance.1`).
    *   **Unit-like structs**: Have no components and occupy no memory. They are used as markers or with traits.
2.  A **method** is an associated function defined within an `impl` block that takes a special first parameter named `self` (or `&self`, `&mut self`). It is called using the dot syntax on an instance of the struct (e.g., `my_struct.method()`). A **type-associated function** is an associated function defined within an `impl` block that does *not* take `self` as its first parameter. It is called using the double colon syntax on the struct's type name (e.g., `StructName::function()`). Type-associated functions are often used as constructors.
3.  You would use:
    *   `&self` when the method needs to **read** data from the struct but **not modify** it, and does not need to take ownership. This is a shared, immutable borrow.
    *   `&mut self` when the method needs to **read and modify** data within the struct, and does not need to take ownership. This is an exclusive, mutable borrow.
    *   `self` when the method needs to take **ownership** of the struct instance itself (consuming it). This allows the method to take ownership of the struct's fields and resources.
4.  The `#[derive(Debug, Copy, Clone)]` attribute tells the Rust compiler to automatically generate implementations for the `Debug`, `Copy`, and `Clone` traits for the struct.
    *   `Debug` allows the struct to be printed using the debug formatter (`println!("{:?}", instance);`).
    *   `Copy` allows values of the struct type to be bitwise-copied on assignment or function calls instead of being moved (if all its fields are also `Copy`).
    *   `Clone` allows explicit deep or shallow copying of the struct value using the `.clone()` method (if all its fields are also `Clone`).
5.  Both `Cell<T>` and `RefCell<T>` are types that provide **interior mutability** in a **single-threaded** context, allowing mutable access to data even when you only have a shared reference (`&`) to the container.
    *   `Cell<T>` is simpler and can *only* be used if the inner type `T` implements the **`Copy` trait**. You interact with `Cell` by getting and setting the *value* using `.get()` and `.set()`. It's useful for simple types like numbers or booleans.
    *   `RefCell<T>` can be used with **any type `T`** (including non-`Copy` types like `String` or `Vec`). You interact with `RefCell` by borrowing **references** to the inner value using `.borrow()` (for `Ref<T>`, a shared borrow) and `.borrow_mut()` (for `RefMut<T>`, a mutable borrow). `RefCell` enforces Rust's borrowing rules at *runtime*, panicking if a violation occurs. You would use `RefCell` when you need to modify a non-`Copy` type within an otherwise immutable struct or when you need to obtain references to the inner data.
   
## Solution

```rust
// src/main.rs

use std::cell::{Cell, RefCell}; // Needed for interior mutability exercises
use std::f64::consts::PI; // Needed for Circle in a previous lab, but good habit for geometric examples
use std::fmt::Debug; // Needed for Debug trait derive

fn main() {
    println!("--- Rust Structs Lab Solutions ---");

    // 1. Exercise: Named-Field Structs: Definition and Instantiation
    println!("\n--- Exercise 1: Named-Field Structs ---");
    // Struct UserProfile defined below main

    let user1 = UserProfile {
        username: String::from("alice99"),
        active: true,
        login_count: 1,
    };

    println!("User 1 Username: {}", user1.username);
    println!("User 1 Login Count: {}", user1.login_count);


    // 2. Exercise: Named-Field Structs: Field Init Shorthand and Struct Update Syntax
    println!("\n--- Exercise 2: Field Init Shorthand and Update Syntax ---");
    // Function build_profile defined below main
    // Struct UserProfile defined below main

    let initial_profile = build_profile(String::from("bob_s"), 5);
    println!("Initial Profile Username: {}, Active: {}, Login Count: {}",
             initial_profile.username, initial_profile.active, initial_profile.login_count);

    // Create a new profile based on initial_profile, updating login_count and active
    let updated_profile = UserProfile {
        login_count: initial_profile.login_count + 10, // Update login_count
        active: false, // Update active
        ..initial_profile // Use remaining fields from initial_profile (moves non-Copy fields like username)
    };
     // Note: initial_profile is now moved and cannot be used further because username (String) is not Copy.
     // If username was Copy (e.g., u32), initial_profile would still be usable.

    println!("Updated Profile Username: {}, Active: {}, Login Count: {}",
             updated_profile.username, updated_profile.active, updated_profile.login_count);


    // 3. Exercise: Tuple-Like Structs
    println!("\n--- Exercise 3: Tuple-Like Structs ---");
    // Struct Color defined below main

    let red = Color(255, 0, 0);
    println!("Red color: R={}, G={}, B={}", red.0, red.1, red.2);


    // 4. Exercise: Unit-Like Structs
    println!("\n--- Exercise 4: Unit-Like Structs ---");
    // Struct ProcessingComplete defined below main

    let status = ProcessingComplete;
    println!("Created a unit-like struct instance.");
    // You can't access fields or do much with it directly, but it exists.


    // 5. Exercise: Defining Methods (&self, &mut self)
    println!("\n--- Exercise 5: Methods (&self, &mut self) ---");
    // UserProfile struct and its impl block defined below main

    let mut user_with_methods = UserProfile::new(String::from("method_user")); // Using new() from later exercise for convenience
    println!("Initial active status: {}", user_with_methods.is_active());
    println!("Initial login count: {}", user_with_methods.login_count);

    user_with_methods.increment_login_count(); // Call mutable method
    println!("Login count after increment: {}", user_with_methods.login_count);

     // Note: is_active() takes &self, increment_login_count() takes &mut self.
     // Rust handles the implicit borrowing in the method call syntax (user.method()).


    // 6. Exercise: Defining Methods (self)
    println!("\n--- Exercise 6: Methods (self) ---");
    // UserProfile struct and its impl block with into_username defined below main

    let user_to_consume = UserProfile::new(String::from("consumer"));
    println!("Username before consuming: {}", user_to_consume.username); // Can access before move

    let consumed_username = user_to_consume.into_username(); // Call method that takes self (moves ownership)
    println!("Username after consuming: {}", consumed_username);

    // The original 'user_to_consume' variable is now moved and unusable.
    // The following line would cause a compile error:
    // println!("User after consuming: {:?}", user_to_consume);

    println!("Original user_to_consume variable is moved and cannot be used after into_username().");


    // 7. Exercise: Type-Associated Functions (::new())
    println!("\n--- Exercise 7: Type-Associated Functions ---");
    // UserProfile struct and its impl block with new() defined below main

    let new_user = UserProfile::new(String::from("type_assoc_user")); // Call the type-associated function
    println!("User created with ::new() - Username: {}, Active: {}, Login Count: {}",
             new_user.username, new_user.active, new_user.login_count);


    // 8. Exercise: Associated Consts
    println!("\n--- Exercise 8: Associated Consts ---");
    // UserProfile struct and its impl block with associated const defined below main

    println!("Default Username associated constant: {}", UserProfile::DEFAULT_USERNAME);


    // 9. Exercise: Generic Structs with Type Parameters
    println!("\n--- Exercise 9: Generic Structs ---");
    // Struct DataContainer<T> defined below main

    let int_container: DataContainer<i32> = DataContainer { data: 42 };
    println!("Int container data: {}", int_container.data);

    let string_container = DataContainer { data: String::from("Generic Data") }; // Rust infers type
    println!("String container data: {}", string_container.data);


    // 10. Exercise: Deriving Common Traits (Debug, Copy, Clone)
    println!("\n--- Exercise 10: Deriving Common Traits ---");
    // Struct Point defined below main with #[derive(Debug, Copy, Clone)]

    let my_point = Point { x: 1.0, y: 2.0 };
    println!("Point using Debug derive: {:?}", my_point); // Requires Debug

    let another_point = my_point; // This is a copy because Point derives Copy
    println!("Another point (copied): {:?}", another_point);
    println!("Original point after copy: {:?}", my_point); // Original is still usable because it was copied


    // 11. Exercise: Interior Mutability: Cell (for Copy types)
    println!("\n--- Exercise 11: Interior Mutability (Cell) ---");
    // Struct StatusTracker and its impl block defined below main

    let tracker = StatusTracker { count: Cell::new(0) };
    println!("Initial tracker count: {}", tracker.get_count()); // get_count takes &self

    tracker.increment_count(); // increment_count takes &self
    tracker.increment_count();
    println!("Tracker count after increments: {}", tracker.get_count());


    // 12. Exercise: Interior Mutability: RefCell (for non-Copy types)
    println!("\n--- Exercise 12: Interior Mutability (RefCell) ---");
    // Struct LogBuffer and its impl block defined below main

    let logger = LogBuffer { messages: RefCell::new(Vec::new()) };
    println!("Log buffer created.");

    logger.add_message(String::from("System starting up...")); // add_message takes &self
    logger.add_message(String::from("Processing request..."));

    println!("Messages in log buffer:");
    logger.print_messages(); // print_messages takes &self


    println!("\n--- Lab Complete ---");
}


// --- Struct Definitions (outside main) ---

// Exercise 1, 2, 5, 6, 7, 8 Struct
#[derive(Debug)] // Added Debug derive for easier printing in some examples
struct UserProfile {
    username: String,
    active: bool,
    login_count: u64,
}

// Exercise 2 Function (uses UserProfile)
fn build_profile(username: String, login_count: u64) -> UserProfile {
    UserProfile {
        username, // Field init shorthand
        active: true, // Default value
        login_count, // Field init shorthand
    }
}

// Exercise 5, 6, 7, 8 impl block for UserProfile
impl UserProfile {
    // Exercise 7: Type-Associated Function (constructor convention)
    pub fn new(username: String) -> UserProfile {
        UserProfile {
            username,
            active: true,
            login_count: 0,
        }
    }

    // Exercise 5: Method with &self
    pub fn is_active(&self) -> bool {
        self.active
    }

    // Exercise 5: Method with &mut self
    pub fn increment_login_count(&mut self) {
        self.login_count += 1;
    }

    // Exercise 6: Method with self (takes ownership)
    pub fn into_username(self) -> String {
        self.username // Returns the username field, consuming self
    }

    // Exercise 8: Associated Const
    pub const DEFAULT_USERNAME: &'static str = "anonymous";
}


// Exercise 3 Struct
struct Color(u8, u8, u8);


// Exercise 4 Struct
struct ProcessingComplete;


// Exercise 9 Struct
#[derive(Debug)] // Added Debug derive for easier printing
struct DataContainer<T> {
    data: T,
}


// Exercise 10 Struct
#[derive(Debug, Copy, Clone)] // Derived common traits
struct Point {
    x: f64,
    y: f64,
}


// Exercise 11 Struct (requires use std::cell::Cell;)
struct StatusTracker {
    count: Cell<u32>,
}

// Exercise 11 impl block for StatusTracker
impl StatusTracker {
    pub fn new(initial_count: u32) -> Self {
        StatusTracker { count: Cell::new(initial_count) }
    }

    pub fn increment_count(&self) { // Takes &self
        let current_count = self.count.get(); // Get value from Cell (&self ok)
        self.count.set(current_count + 1); // Set value in Cell (&self ok)
    }

    pub fn get_count(&self) -> u32 { // Takes &self
        self.count.get() // Get value from Cell (&self ok)
    }
}

// Exercise 12 Struct (requires use std::cell::RefCell;)
struct LogBuffer {
    messages: RefCell<Vec<String>>,
}

// Exercise 12 impl block for LogBuffer
impl LogBuffer {
    pub fn new() -> Self {
        LogBuffer { messages: RefCell::new(Vec::new()) }
    }

    pub fn add_message(&self, msg: String) { // Takes &self
        let mut messages_mut = self.messages.borrow_mut(); // Get mutable borrow from RefCell (&self ok)
        messages_mut.push(msg); // Modify the Vec through the mutable borrow
    }

    pub fn print_messages(&self) { // Takes &self
        let messages_ref = self.messages.borrow(); // Get shared borrow from RefCell (&self ok)
        for msg in messages_ref.iter() { // Iterate over the Vec through the shared borrow
            println!("  Log: {}", msg);
        }
    }
}
```

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025.**
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*