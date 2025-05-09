**Lab 13: Generics, Traits, and Dispatch (Static vs. Dynamic)**

We should appreciate Rust not only in its memory safety but also for its robust system for writing flexible and reusable code through generics and traits. This lab is about defining types and functions that work with multiple data types (`generics`), specifying shared behavior across different types (`traits`), and understanding how Rust chooses which specific code implementation to run when you call a trait method (`static` vs. `dynamic` dispatch).

By the end of this lab, you will be able to:

*   Define and use generic structs and functions.
*   Define simple traits and implement them for custom types.
*   Use trait bounds to constrain generic parameters.
*   Understand and apply static dispatch using trait bounds.
*   Understand and apply dynamic dispatch using trait objects (`dyn Trait`).
*   Recognize the trade-offs between static and dynamic dispatch.

This lab builds upon your understanding of basic Rust syntax and types and introduces key concepts for writing idiomatic and flexible Rust code.

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, functions, `println!`, structs, enums).
    *   Familiarity with data types like integers, floats, and strings.
    *   Basic understanding of data structures like `Vec`.
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. Check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial generics lab setup`, `feat: complete generic struct exercise`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new generics_trait_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd generics_trait_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `generics_trait_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file. Use comments (`//`) to separate and label the code for each exercise. You will also define structs and traits *outside* of the `main` function.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`, unless otherwise specified.

1.  **Generic Struct**
    Create a struct that can hold values of different types using generic parameters.

    *   **Exercise Type Goal:** Define and use a struct with generic type parameters.
    *   Define a simple struct `Pair<T, U>` *outside* of `main` with two fields: `first` of type `T` and `second` of type `U`.
    *   In `main`, create two instances of `Pair`:
        *   One instance holding an `i32` and an `f64`.
        *   One instance holding a `String` and a `bool`.
    *   Access and print the fields of both `Pair` instances.
    *   **Hint:** Use angle brackets `<T, U>` after the struct name to declare generic parameters.
    *   **Crucial Lines:**
        ```rust
        struct Pair<T, U> {
            first: T,
            second: U,
        }
        // In main:
        let number_pair = Pair { first: 10, second: 3.14 };
        ```
    *   **Verification:** Run `cargo run`. You should see the field values printed for both `Pair` instances.

2.  **Generic Function (No Constraints)**
    Create a function that can accept arguments of different types using a simple generic parameter.

    *   **Exercise Type Goal:** Define and use a function with a generic type parameter. Understand that the generic parameter alone doesn't imply specific capabilities (like printing with `{:?}`).
    *   Define a function `print_any<T>` *outside* of `main` that takes one parameter `value` of type `T`.
    *   Inside `print_any`, try to print `value` using the debug formatter: `println!("{:?}", value);`.
    *   **Observe:** You might get a compile error.
    *   **Discuss:** Why might this fail? The `println!("{:?}", ...)` macro requires the type being printed to implement the `std::fmt::Debug` trait. The generic parameter `T` in `fn print_any<T>` doesn't *guarantee* that `T` implements `Debug`.
    *   **Correct:** Add a **trait bound** to the generic parameter `T` in the function signature, requiring it to implement the `Debug` trait: `fn print_any<T: std::fmt::Debug>(value: T)`.
    *   In `main`, call `print_any` with different types that *do* implement `Debug`, such as an `i32`, a `String`, and a boolean.
    *   **Verification:** Run `cargo run`. The corrected code should compile, and you should see the different values printed via the generic function.

3.  **Static Dispatch (Single Trait)**
    Define a trait, implement it for a type, and define a generic function constrained by that trait. This demonstrates static dispatch.

    *   **Exercise Type Goal:** Define a trait, implement it, and use a generic function with a single trait bound, resulting in static dispatch.
    *   Define a trait `Shape` *outside* of `main` with one method: `fn area(&self) -> f64;`.
    *   Define a struct `Rectangle` *outside* of `main` with `width` and `height` fields (`f64`).
    *   Implement the `Shape` trait for the `Rectangle` struct. Calculate the area as `width * height`.
    *   Define a function `print_area<S: Shape>` *outside* of `main` that takes a generic parameter `S` with the trait bound `Shape`. Inside, call `item.area()` and print the result.
    *   In `main`, create a `Rectangle` instance and call `print_area` with it.
    *   **Discuss:** This is **static dispatch**. At compile time, the Rust compiler knows that `S` is `Rectangle` and can directly call the `Rectangle`'s specific `area` implementation. There's no runtime lookup needed.
    *   **Verification:** Run `cargo run`. The code should compile, and the area of the rectangle should be printed via the generic function.

4.  **Static Dispatch (Multiple Traits)**
    Define a second trait, implement both for a type, and define a generic function constrained by both traits.

    *   **Exercise Type Goal:** Use multiple trait bounds on a generic parameter, still resulting in static dispatch.
    *   Define a second trait `PrintableInfo` *outside* of `main` with one method: `fn print_details(&self);`.
    *   Define a struct `Circle` *outside* of `main` with a `radius` field (`f64`). Implement the `Shape` trait for `Circle` (area is `PI * radius * radius`). You'll need `use std::f64::consts::PI;`.
    *   Implement the `PrintableInfo` trait for the `Circle` struct. Inside `print_details`, print the circle's radius and calculated area.
    *   Define a function `analyze_shape<T: Shape + PrintableInfo>` *outside* of `main` that takes a generic parameter `T` constrained by *both* the `Shape` and `PrintableInfo` traits using the `+` syntax. Inside, call both `item.area()` (maybe print it) and `item.print_details()`.
    *   In `main`, create a `Circle` instance and call `analyze_shape` with it.
    *   **Discuss:** This is still **static dispatch**. The compiler knows `T` is `Circle` and calls the specific `Circle` implementations for both `area` and `print_details` directly. Multiple trait bounds are a common way to require types to have several different capabilities.
    *   **Verification:** Run `cargo run`. The code should compile, and both the area and the details of the circle should be printed via the generic function.

5.  **Dynamic Dispatch (Single Trait)**
    Define a function that accepts a trait object reference using `&dyn Trait`. This demonstrates dynamic dispatch.

    *   **Exercise Type Goal:** Understand and use dynamic dispatch with a single trait object. Recognize the `&dyn Trait` syntax.
    *   Use the `Shape` trait and the `Rectangle` and `Circle` structs from previous exercises.
    *   Define a function `print_area_dynamic` *outside* of `main` that takes **one parameter**: `shape: &dyn Shape`. This is a reference to a trait object that implements the `Shape` trait. Inside, call `shape.area()` and print the result.
    *   In `main`, create a `Rectangle` instance and a `Circle` instance.
    *   Get references to these instances *as* the `&dyn Shape` trait object type: `let rect_shape: &dyn Shape = &my_rectangle_instance;` and `let circle_shape: &dyn Shape = &my_circle_instance;`.
    *   Call `print_area_dynamic` with both `rect_shape` and `circle_shape`.
    *   **Discuss:** This is **dynamic dispatch**. The compiler does *not* know the concrete type (`Rectangle` or `Circle`) when compiling `print_area_dynamic`. When `shape.area()` is called at runtime, the program looks up the correct `area` implementation for the specific type (stored in a vtable associated with the trait object) and calls it. This allows `print_area_dynamic` to work with any type implementing `Shape`, even if that type isn't known until runtime.
    *   **Verification:** Run `cargo run`. The code should compile, and the correct areas for both the rectangle and the circle should be printed by the `print_area_dynamic` function.

6.  **Dynamic Dispatch (Multiple Traits)**
    Define a function that accepts a trait object reference implementing multiple traits using `&dyn (Trait1 + Trait2)`.

    *   **Exercise Type Goal:** Use dynamic dispatch with a trait object that requires multiple traits.
    *   Use the `Shape` and `PrintableInfo` traits and the `Circle` struct from previous exercises (since `Circle` implements both).
    *   Define a function `analyze_shape_dynamic` *outside* of `main` that takes **one parameter**: `item: &dyn (Shape + PrintableInfo)`. This is a reference to a trait object that implements *both* `Shape` and `PrintableInfo`. Inside, call both `item.area()` and `item.print_details()`.
    *   In `main`, create a `Circle` instance.
    *   Get a reference to this instance *as* the `&dyn (Shape + PrintableInfo)` trait object type: `let circle_analyzable: &dyn (Shape + PrintableInfo) = &my_circle_instance;`.
    *   Call `analyze_shape_dynamic` with `circle_analyzable`.
    *   **Discuss:** This is still **dynamic dispatch**. The trait object bundles together the necessary information (the data pointer and pointers to the vtables for both traits) to allow runtime lookup of methods from both `Shape` and `PrintableInfo`. This function can accept *any* type implementing *both* traits via a trait object reference.
    *   **Verification:** Run `cargo run`. The code should compile, and the dynamic function should correctly analyze the circle using methods from both traits.

**Running the Application**

As you complete each exercise, run `cargo run` to verify your code before moving to the next one.

**Key Concepts Review**

*   **Generics (`<T>`, `<U>` etc.)**: Allow types and functions to work with arbitrary types specified at compile time.
*   **Traits (`trait MyTrait { ... }`)**: Define a set of behaviors (method signatures) that types can implement.
*   **`impl MyTrait for MyType { ... }`**: Provides the concrete implementation of a trait's methods for a specific type.
*   **Trait Bounds (`<T: MyTrait>`, `<T: Trait1 + Trait2>`)**: Constrain generic parameters to types that implement a specific trait or set of traits.
*   **Static Dispatch**: The compiler knows the concrete type at compile time and directly calls the specific method implementation. This is the default for generic functions with trait bounds. It's generally faster as there's no runtime overhead.
*   **Dynamic Dispatch**: The compiler does not know the concrete type at compile time. The specific method implementation is looked up at runtime via a vtable. This is used with **trait objects** (`&dyn Trait`, `Box<dyn Trait>`). It provides flexibility (can work with different concrete types dynamically) but has a small runtime performance cost.
*   **Trait Objects (`dyn Trait`)**: Types that represent *any* type implementing a specific trait (or set of traits). They must be used behind a pointer (like `&`, `&mut`, or `Box`) because their size is not known at compile time.

**Challenges / Next Steps**

1.  **Newtype Pattern:** Define a "newtype" struct (`struct Meters(f64);`) that wraps a primitive type. Implement a simple trait for it. How does this differ from a type alias?
2.  **Associated Types:** Explore traits with associated types (`trait Collection { type Item; fn get(&self, index: usize) -> Option<&Self::Item>; }`). How do associated types make traits more flexible?
3.  **Default Trait Methods:** Add a default implementation to a trait method (`trait MyTrait { fn required_method(&self); fn optional_method(&self) { /* default */ } }`). How does this work?

**Troubleshooting**

*   **"the trait `...` is not implemented for `...`"**: You are trying to use a generic type or a trait object in a way that requires a trait implementation, but the type you are providing doesn't implement that trait. Or, your generic function has a trait bound, and you're calling it with a type that doesn't meet the bound. Ensure the required `impl Trait for Type` block exists.
*   **"doesn't have a size known at compile-time"**: You are likely trying to use a `dyn Trait` directly (e.g., return `dyn Trait` or store `dyn Trait` in a struct field) without putting it behind a pointer (`&`, `&mut`, `Box`). Trait objects are dynamically sized types (DSTs).
*   **"multiple applicable items in scope"**: This can sometimes happen when trait methods have conflicting names or when using the `use` keyword with traits or functions. Use fully qualified syntax (`<Type as Trait>::method(...)`) or method call syntax (`value.method()`) to clarify.

**Conclusion**

You have successfully worked with generics, traits, and the critical distinction between static and dynamic dispatch in Rust. These tools are fundamental for writing adaptable and expressive Rust code that can work with a variety of types while enforcing shared behaviors.

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete generics trait lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Final Solution Code (src/main.rs)**

```rust
// src/main.rs

use std::f64::consts::PI; // Needed for Circle area calculation
use std::fmt::Debug; // Needed for Debug trait bound in print_any

fn main() {
    println!("--- Rust Generics, Traits, and Dispatch Lab Solutions ---");

    // 1. Exercise: Generic Struct
    println!("\n--- Exercise 1: Generic Struct ---");
    // Struct Pair<T, U> defined below main

    let number_pair: Pair<i32, f64> = Pair {
        first: 10,
        second: 3.14,
    };
    println!(
        "Number pair: ({}, {})",
        number_pair.first, number_pair.second
    );

    let string_bool_pair: Pair<String, bool> = Pair {
        first: String::from("Generic"),
        second: true,
    };
    println!(
        "String/Bool pair: ({}, {})",
        string_bool_pair.first, string_bool_pair.second
    );

    // 2. Exercise: Generic Function (No Constraints)
    println!("\n--- Exercise 2: Generic Function ---");
    // Function print_any<T: Debug> defined below main

    print_any(123); // Call with i32
    print_any(String::from("Generic function works with Strings")); // Call with String
    print_any(false); // Call with bool

    // 3. Exercise: Static Dispatch (Single Trait)
    println!("\n--- Exercise 3: Static Dispatch (Single) ---");
    // Trait Shape and struct Rectangle defined below main
    // Function print_area<S: Shape> defined below main

    let my_rectangle = Rectangle {
        width: 5.0,
        height: 10.0,
    };
    print_area(my_rectangle); // Static dispatch: compiler knows it's a Rectangle

    // 4. Exercise: Static Dispatch (Multiple Traits)
    println!("\n--- Exercise 4: Static Dispatch (Multiple) ---");
    // Trait PrintableInfo and struct Circle defined below main
    // Function analyze_shape<T: Shape + PrintableInfo> defined below main

    let my_circle = Circle { radius: 3.0 };
    analyze_shape(my_circle); // Static dispatch: compiler knows it's a Circle, calls methods from both traits

    // 5. Exercise: Dynamic Dispatch (Single Trait)
    println!("\n--- Exercise 5: Dynamic Dispatch (Single) ---");
    // Function print_area_dynamic(shape: &dyn Shape) defined below main
    // Re-using Rectangle and Circle from previous exercises

    let rect_instance_for_dynamic = Rectangle {
        width: 2.0,
        height: 4.0,
    };
    let circle_instance_for_dynamic = Circle { radius: 5.0 };

    // Get references as trait objects
    let rect_shape: &dyn Shape = &rect_instance_for_dynamic;
    let circle_shape: &dyn Shape = &circle_instance_for_dynamic;

    // Call the dynamic dispatch function
    print_area_dynamic(rect_shape); // Dynamic dispatch: lookup area method at runtime
    print_area_dynamic(circle_shape); // Dynamic dispatch: lookup area method at runtime

    // 6. Exercise: Dynamic Dispatch (Multiple Traits)
    println!("\n--- Exercise 6: Dynamic Dispatch (Multiple) ---");
    // Trait AnalyzableShape defined below main (inherits from Shape + PrintableInfo)
    // Function analyze_shape_dynamic(item: &dyn AnalyzableShape) defined below main
    // Re-using Circle from previous exercises (implements Shape and PrintableInfo, thus implements AnalyzableShape)

    let circle_instance_for_dynamic_multi = Circle { radius: 4.0 };

    // Get a reference as a trait object implementing the NEW combined trait
    // Corrected approach: Use the combined trait AnalyzableShape
    let circle_analyzable: &dyn AnalyzableShape = &circle_instance_for_dynamic_multi;

    // Call the dynamic dispatch function
    analyze_shape_dynamic(circle_analyzable); // Dynamic dispatch: lookup methods from the combined trait via vtable

    println!("\n--- Lab Complete ---");
}

// --- Function Definitions (outside main) ---

// Exercise 2 Function
fn print_any<T: Debug>(value: T) {
    // Added Debug trait bound
    println!("Value received by generic function: {:?}", value);
}

// Exercise 3, 4, 5, 6 Traits and Structs
trait Shape {
    fn area(&self) -> f64;
}

#[derive(Debug, Copy, Clone)] // Added Copy/Clone for easy use in main
struct Rectangle {
    width: f64,
    height: f64,
}

impl Shape for Rectangle {
    fn area(&self) -> f64 {
        self.width * self.height
    }
}

trait PrintableInfo {
    fn print_details(&self);
}

#[derive(Debug, Copy, Clone)] // Added Copy/Clone for easy use in main
struct Circle {
    radius: f64,
}

impl Shape for Circle {
    fn area(&self) -> f64 {
        PI * self.radius * self.radius
    }
}

impl PrintableInfo for Circle {
    fn print_details(&self) {
        println!("  Shape: Circle");
        println!("  Radius: {}", self.radius);
        println!("  Calculated Area: {}", self.area()); // Can call other trait methods from here
    }
}

// Exercise 6: Define a new trait that combines Shape and PrintableInfo
// This is the standard way to create a trait object requiring multiple non-auto traits.
trait AnalyzableShape: Shape + PrintableInfo {}

// Corrected: Explicitly implement the new combined trait for Circle
// Even though it's an empty impl block, it's required to tell the compiler that Circle
// fulfills the requirements to BE an AnalyzableShape.
impl AnalyzableShape for Circle {}

// --- Function Definitions (outside main) ---

// Exercise 3 Function (Static Dispatch Single)
fn print_area<S: Shape>(item: S) {
    // Takes generic type S with Shape bound
    println!("Static Dispatch: Area is {}", item.area());
}

// Exercise 4 Function (Static Dispatch Multiple)
fn analyze_shape<T: Shape + PrintableInfo>(item: T) {
    // Takes generic type T with Shape + PrintableInfo bounds
    println!("Static Dispatch (Multiple Bounds): Analyzing shape...");
    item.print_details(); // Calls PrintableInfo method
    println!("  Area confirmed: {}", item.area()); // Calls Shape method
}

// Exercise 5 Function (Dynamic Dispatch Single)
fn print_area_dynamic(shape: &dyn Shape) {
    // Takes a reference to a Shape trait object
    println!("Dynamic Dispatch (Single): Area is {}", shape.area()); // Method call happens via vtable lookup at runtime
}

// Exercise 6 Function (Dynamic Dispatch Multiple)
// Takes a reference to the NEW combined trait object: &dyn AnalyzableShape
fn analyze_shape_dynamic(item: &dyn AnalyzableShape) {
    // Takes a reference to a trait object implementing the combined trait
    println!("Dynamic Dispatch (Multiple): Analyzing shape dynamically...");
    item.print_details(); // Calls PrintableInfo method via vtable (available because AnalyzableShape: PrintableInfo)
    println!("  Area confirmed: {}", item.area()); // Calls Shape method via vtable (available because AnalyzableShape: Shape)
}

// Exercise 1 Struct
#[derive(Debug)] // Derive Debug for easy printing
struct Pair<T, U> {
    first: T,
    second: U,
}

```

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete generics trait lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Check Questions (To Test Understanding)**

1.  What is the primary difference between a generic type parameter (like `T` in `struct Pair<T>`) and a trait object (like `dyn Trait`)?
2.  When defining a generic function `fn process<T>(item: T)`, why might you need to add a trait bound like `T: Debug`?
3.  Explain the difference between static dispatch and dynamic dispatch in Rust.
4.  If you have a struct `MyStruct` that implements two traits, `TraitA` and `TraitB`, how would you define a function using static dispatch that requires the parameter to implement *both* traits?
5.  Using the same `MyStruct` from the previous question, how would you define a function using dynamic dispatch that accepts a reference to any type implementing *both* `TraitA` and `TraitB`?

**Detailed Answers to Check Questions**

1.  A **generic type parameter** (`T`) represents a **concrete type** that is determined at **compile time**. The compiler generates specific versions of the code for each concrete type used. A **trait object** (`dyn Trait`) represents **any type that implements a specific trait (or set of traits)**. The concrete type is **not known at compile time**; it is determined at runtime.
2.  You might need to add a trait bound like `T: Debug` to a generic function `fn process<T>(item: T)` if the code *inside* the function calls methods or uses syntax that requires the generic type `T` to have specific capabilities defined by the `Debug` trait (like being printable with the debug formatter `{:?}`). Without the bound, the compiler doesn't know that `T` will have the required `Debug` capabilities and will produce a compile error.
3.  **Static Dispatch**: The compiler resolves the method call at **compile time**. It knows the concrete type of the object the method is being called on and can directly jump to the correct method implementation. This is the default for generic functions with trait bounds. It's fast and allows for optimizations like inlining.
    **Dynamic Dispatch**: The method call is resolved at **runtime**. The program uses information stored with the object (in a vtable associated with the trait object) to find the correct method implementation. This is used with trait objects (`&dyn Trait`, `Box<dyn Trait>`). It offers flexibility (can work with types unknown at compile time) at the cost of a small runtime lookup overhead.
4.  To define a function using static dispatch that requires the parameter to implement both `TraitA` and `TraitB`, you use **multiple trait bounds** on the generic type parameter, separated by `+`:
    ```rust
    fn process_both_static<T: TraitA + TraitB>(item: T) {
        // Inside, you can call methods from both TraitA and TraitB on 'item'
    }
    ```
5.  To define a function using dynamic dispatch that accepts a reference to any type implementing *both* `TraitA` and `TraitB`, you use a **trait object** parameter with combined trait bounds:
    ```rust
    fn process_both_dynamic(item: &dyn (TraitA + TraitB)) {
        // Inside, you can call methods from both TraitA and TraitB on 'item'
        // This call will be dynamically dispatched at runtime.
    }
    ```
    The parameter type `&dyn (TraitA + TraitB)` signifies a reference to an object whose concrete type is unknown at compile time but is guaranteed to implement both `TraitA` and `TraitB`.


---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025.**
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*    