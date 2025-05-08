**Lab 11: Expressions and more...**

Understanding how to structure expressions, control the flow of execution with conditionals and loops, and handle data transformations is fundamental to programming in any language. In Rust, these concepts are designed with clarity and safety in mind. This lab provides a hands-on tour of Rust's syntax for expressions, operator precedence, conditional statements (`if`, `match`), loops (`loop`, `while`, `for`), type casting, and closures.

By the end of this lab, you will be able to:

*   Recognize and use expressions effectively.
*   Understand and apply Rust's operator precedence and associativity rules.
*   Implement conditional logic using `if`, `match`, and `if let`.
*   Choose and use the appropriate loop construct for different scenarios.
*   Perform explicit type casting and understand potential issues.
*   Define and use closures to capture and work with their environment.

This lab reinforces basic Rust syntax while introducing key constructs that enable flexible and powerful programming patterns.

**Prerequisites**

*   **Software:**
    *   Rust toolchain (`rustc`, `cargo`) installed. We will use Rust `edition = "2024"`. This lab was tested with `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`.
    *   Text editor or IDE (e.g., VS Code with rust-analyzer).
*   **Knowledge:**
    *   Basic Rust syntax (variables, `println!`).
    *   A basic understanding of data types like integers, booleans, and strings.
*   **Setup Verification:** Open your terminal and run `rustc --version` and `cargo --version` to confirm your installation.

**Setup Instructions**

Before starting, ensure you are in your designated training repository. Check that you are on the appropriate branch and that you have no uncommitted changes (`git status`). It's recommended to commit frequently using [Conventional Commits](https://www.conventionalcommits.org/) to track your progress (e.g., `feat: initial syntax lab setup`, `feat: complete expressions exercise`).

1.  **Create a New Cargo Project:**
    Navigate to where you want to create your project in your terminal.
    ```bash
    cargo new syntax_lab --edition 2024
    ```
    The `edition` flag can be set to `2015`, `2018`, `2021`, or `2024`. We're using the latest for this lab.

2.  **Navigate into the Project Directory:**
    ```bash
    cd syntax_lab
    ```

3.  **Open the Project in Your Editor:**
    Open the `syntax_lab` directory in your preferred text editor or IDE. You will be primarily working with the `src/main.rs` file.

You are now ready to begin the exercises. Implement the code for each exercise sequentially in the `main` function within your `src/main.rs` file. Use comments (`//`) to separate and label the code for each exercise.

**Lab Steps (Exercises)**

Implement the code for each exercise inside the `main` function in `src/main.rs`.

1.  **Expressions: Block Expressions**
    Rust is an expression-oriented language. This means that many constructs that would be statements in other languages are expressions in Rust, and they evaluate to a value. A block of code delimited by curly braces `{}` is an expression.

    *   **Exercise Type Goal:** Understand that code blocks `}` can return a value.
    *   Create a variable `result`. Assign to it the value of a block expression. Inside the block, perform a simple calculation (e.g., `10 + 5;`) and make the last line of the block an expression (without a semicolon) that you want to be the block's return value (e.g., `20 * 2;`).
    *   Print the value of `result`.
    *   **Hint:** Remember that the value of a block expression is the value of its *last expression* (without a semicolon).
    *   **Crucial Line Example:**
        ```rust
        let result = {
            let a = 10;
            let b = 5;
            a + b // This is the expression whose value the block returns
        };
        ```
    *   **Verification:** Run `cargo run`. You should see the value of the last expression in the block printed.

2.  **Precedence and Associativity: Arithmetic Operators**
    Operator precedence determines the order in which operators are evaluated (e.g., multiplication before addition). Associativity determines evaluation order for operators with the same precedence (e.g., left-to-right for addition).

    *   **Exercise Type Goal:** Observe the default evaluation order of arithmetic operators.
    *   Write an expression involving addition, subtraction, multiplication, and division (e.g., `10 + 5 * 2 - 4 / 2`).
    *   Assign the result to a variable and print it.
    *   Mentally trace the expected evaluation order based on standard arithmetic precedence (*/ before +-).
    *   **Verification:** Run `cargo run`. Confirm the calculated result matches your expectation based on precedence.

3.  **Precedence and Associativity: Logical Operators**
    Logical operators (`&&` for AND, `||` for OR, `!` for NOT) also have precedence and associativity. `!` has the highest, followed by `&&`, then `||`.

    *   **Exercise Type Goal:** Observe the default evaluation order of logical operators.
    *   Write a boolean expression involving `true`, `false`, `&&`, `||`, and `!`. For example, `true || false && !true`.
    *   Assign the result to a boolean variable and print it.
    *   Mentally trace the expected evaluation order based on logical operator precedence.
    *   **Hint:** Remember `&&` binds tighter than `||`.
    *   **Verification:** Run `cargo run`. Confirm the boolean result matches your expectation.

4.  **Conditional Logic: `if` and `match`**
    `if` statements and `match` expressions are used for branching logic. `match` is particularly powerful for handling multiple possible values of a variable.

    *   **Exercise Type Goal:** Practice basic branching with `if` and `match`.
    *   Declare an integer variable `number`.
    *   Use an `if/else if/else` structure to check if `number` is positive, negative, or zero, and print an appropriate message.
    *   Use a `match` expression on the same `number` variable to handle specific cases (e.g., 1, 5, or any other number), and print a different message for each case.
    *   **Hints:**
        *   `if` conditions must be `bool`.
        *   `match` is exhaustive; you must cover all possible values (the `_` wildcard is useful for "anything else").
    *   **Crucial Line Examples:**
        ```rust
        if number > 0 { ... } else if number < 0 { ... } else { ... }
        match number { 1 => { ... }, 5 => { ... }, _ => { ... } }
        ```
    *   **Verification:** Run `cargo run`. Verify the output matches the value of `number` and your conditional logic.

5.  **Conditional Logic: `if let`**
    `if let` is a convenient syntax sugar for matching a single pattern, often used with `Option` or `Result` to handle the `Some`/`Ok` case concisely while ignoring the `None`/`Err` case.

    *   **Exercise Type Goal:** Use `if let` to handle an `Option` value.
    *   Declare an `Option<i32>` variable. Set its value to `Some(10)`.
    *   Use `if let` to check if the `Option` contains a value (`Some(value)`). If it does, print the extracted `value`.
    *   Add an `else` block to the `if let` to handle the `None` case and print a different message.
    *   Change the `Option` variable to `None` and run the code again.
    *   **Hint:** `if let Some(variable_name) = option_variable { ... }`.
    *   **Verification:** Run `cargo run` twice (once with `Some`, once with `None`). Confirm the correct block executes each time.

6.  **Loops: `loop` (Infinite Loop)**
    The `loop` keyword creates an infinite loop. You typically use `break` to exit it and optionally `continue` to skip to the next iteration.

    *   **Exercise Type Goal:** Use `loop` to repeat an action until a condition is met internally via `break`.
    *   Initialize a mutable counter variable to 0.
    *   Start a `loop { ... }`.
    *   Inside the loop, increment the counter and print its value.
    *   Add an `if` condition inside the loop to check if the counter has reached a certain value (e.g., 5). If it has, use `break;` to exit the loop.
    *   **Verification:** Run `cargo run`. The output should show the counter incrementing from 1 up to 5, and then the program should exit the loop.

7.  **Loops: `while` (Conditional Loop)**
    A `while` loop continues as long as a condition remains true.

    *   **Exercise Type Goal:** Use `while` to loop based on a boolean condition.
    *   Initialize a mutable counter variable to a starting value (e.g., 3).
    *   Start a `while` loop that continues as long as the counter is greater than 0.
    *   Inside the loop, print the current value of the counter.
    *   Decrement the counter.
    *   **Verification:** Run `cargo run`. The output should show the counter counting down from its starting value to 1, and then the program should exit.

8.  **Loops: `for` (Iterator Loop)**
    The `for` loop is the most common loop in Rust. It iterates over anything that implements the `IntoIterator` trait, such as ranges, vectors, maps, etc.

    *   **Exercise Type Goal:** Use `for` to iterate over a range of numbers.
    *   Use a `for` loop to iterate over a range of integers (e.g., from 1 to 5 inclusive).
    *   Inside the loop, print the current number.
    *   **Hint:** Use `start..end` for an exclusive range or `start..=end` for an inclusive range.
    *   **Crucial Line Example:**
        ```rust
        for i in 1..=5 {
            // ... print i ...
        }
        ```
    *   **Verification:** Run `cargo run`. The output should show numbers 1 through 5 printed.

9.  **Type Casting**
    Explicitly converting a value from one type to another using the `as` keyword. Be mindful of potential data loss or changes in representation (e.g., casting a float to an integer truncates).

    *   **Exercise Type Goal:** Practice explicit type casting and observe potential data changes.
    *   Declare a floating-point variable (e.g., `f64`).
    *   Declare a large integer variable (e.g., `i64`).
    *   Cast the floating-point variable to an integer type (e.g., `i32`). Print the result. Observe how the decimal part is handled.
    *   Cast the large integer variable to a smaller integer type (e.g., `i8`). Print the result. Observe potential truncation or wrapping if the value exceeds the smaller type's range.
    *   **Hint:** Use `value as TargetType`.
    *   **Verification:** Run `cargo run`. Observe the printed values after casting and explain why they changed as they did.

10. **Closures: Basic Closure**
    Closures are anonymous functions that can capture values from their surrounding environment.

    *   **Exercise Type Goal:** Define and call a simple closure that doesn't capture its environment.
    *   Define a variable and assign a closure to it. The closure should take no arguments and simply print a message (e.g., `let my_closure = || { println!("Hello from closure!"); };`).
    *   Call the closure using its variable name followed by parentheses: `my_closure();`.
    *   **Verification:** Run `cargo run`. The message inside the closure should be printed.

11. **Closures: Capturing Environment (Immutable Borrow)**
    Closures can immutably borrow values from the scope where they are defined.

    *   **Exercise Type Goal:** Create a closure that immutably borrows a variable from its environment.
    *   Declare a variable (e.g., `let greeting = String::from("Hi");`).
    *   Define a closure that uses the `greeting` variable inside its body.
    *   Call the closure.
    *   **Discuss:** How did the closure access `greeting`? It implicitly immutably borrowed it. Can you still use `greeting` after calling the closure? Yes, because the closure only borrowed it immutably.
    *   **Verification:** Run `cargo run`. The closure should print the greeting, and you should be able to print `greeting` again afterwards.

12. **Closures: Capturing Environment (Mutable Borrow or Ownership)**
    Closures can also mutable borrow or take ownership of variables from their environment, often requiring the `move` keyword or mutable contexts.

    *   **Exercise Type Goal:** Create a closure that mutably borrows or takes ownership of a variable.
    *   Declare a *mutable* variable (e.g., `let mut counter = 0;`).
    *   Define a closure that modifies the `counter` variable (e.g., increments it).
    *   **Observe:** The compiler might require the `move` keyword or indicate that the closure needs to be mutable (`FnMut`). If the closure only modifies, it likely needs to be `FnMut` and capture by mutable reference. Add the `move` keyword before the `|...|` if needed to explicitly move ownership into the closure.
    *   Call the closure several times.
    *   **Discuss:** How did the closure interact with `counter`? If `move` was used, ownership was transferred. If `move` wasn't used but it compiled, it likely captured by mutable reference. What happens to `counter` after the closure is defined/called?
    *   **Verification:** Run `cargo run`. The closure should modify the counter each time it's called.

**Running the Application**

As you complete each exercise, run `cargo run` to verify your code before moving to the next one.

**Key Concepts Review**

*   **Expressions vs. Statements**: Expressions evaluate to a value; statements perform an action and don't return a value (or return the unit type `()`). Blocks `{}` are expressions.
*   **Operator Precedence**: The order in which different types of operators are evaluated.
*   **Associativity**: The order of evaluation for operators of the same precedence.
*   **`if` / `else if` / `else`**: Basic conditional branching.
*   **`match`**: Powerful pattern matching for handling multiple possibilities exhaustively.
*   **`if let`**: Concise syntax for matching a single pattern, often with `Option` or `Result`.
*   **`loop`**: Creates an infinite loop, exited with `break`.
*   **`while`**: Loops as long as a condition is true.
*   **`for`**: Iterates over anything that can be turned into an iterator (ranges, collections).
*   **`as`**: Used for explicit type casting. Be aware of potential data loss.
*   **Closures**: Anonymous functions defined inline, capable of capturing variables from their surrounding scope (environment).
*   **Closure Capture**: Closures capture variables by immutable reference (`&T`), mutable reference (`&mut T`), or by value (ownership, using `move`).

**Challenges / Next Steps**

1.  **Loop Labels:** Explore using loop labels (`'label: loop { ... break 'label; }`) to break out of nested loops.
2.  **Type Alias:** Define a type alias (`type MyInt = i32;`) and use it in your code. How does it differ from a newtype struct?
3.  **Closures as Function Arguments:** Write a function that accepts a closure as an argument. Research the `Fn`, `FnMut`, and `FnOnce` traits, which define the kinds of closures a function can accept based on how they capture/use variables.

**Troubleshooting**

*   **Syntax Errors**: Check for missing semicolons (especially after statements *within* blocks intended to return a value, or after expression statements). Ensure correct parentheses and curly braces.
*   **Type Mismatches**: Ensure variables and expressions have the expected types, especially during assignments or when using `as` for casting.
*   **Borrowing Errors (in Closures)**: If your closure needs to modify a captured variable and you get a borrowing error, ensure the variable is mutable (`let mut ...`), and the closure might need to be marked as `mut` (if using it as a `FnMut`) or use the `move` keyword if transferring ownership is intended.

**Conclusion**

You have now practiced fundamental Rust syntax and control flow constructs. Mastering expressions, conditionals, loops, type casting, and closures provides the building blocks for writing more complex Rust programs.

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025.
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.

Before concluding your session, remember to commit your changes (`git commit -am "feat: complete syntax lab"` - using conventional commits) and synchronize them with your remote repository (`git push`).

**Final Solution Code (src/main.rs)**

```rust
// src/main.rs

fn main() {
    println!("--- Rust Syntax Lab Solutions ---");

    // 1. Exercise: Expressions: Block Expressions
    println!("\n--- Exercise 1: Block Expressions ---");
    let block_value = {
        let a = 10;
        let b = 20;
        let intermediate = a + b;
        intermediate * 2 // No semicolon means this is the return value of the block
    };
    println!("Value of the block expression: {}", block_value);


    // 2. Exercise: Precedence and Associativity: Arithmetic Operators
    println!("\n--- Exercise 2: Arithmetic Precedence ---");
    // Standard precedence: */ before +-
    // (5 * 2) = 10
    // (4 / 2) = 2
    // (10 + 10) = 20
    // (20 - 2) = 18
    let arithmetic_result = 10 + 5 * 2 - 4 / 2;
    println!("Result of arithmetic expression (10 + 5 * 2 - 4 / 2): {}", arithmetic_result);


    // 3. Exercise: Precedence and Associativity: Logical Operators
    println!("\n--- Exercise 3: Logical Precedence ---");
    // Precedence: ! > && > ||
    // !true = false
    // false && false = false
    // true || false = true
    let logical_result = true || false && !true;
    println!("Result of logical expression (true || false && !true): {}", logical_result);


    // 4. Exercise: Conditional Logic: if and match
    println!("\n--- Exercise 4: if and match ---");
    let number = 7;

    // Using if/else if/else
    if number > 0 {
        println!("if/else: The number is positive.");
    } else if number < 0 {
        println!("if/else: The number is negative.");
    } else {
        println!("if/else: The number is zero.");
    }

    // Using match
    match number {
        1 => {
            println!("match: The number is one.");
        },
        5 => {
            println!("match: The number is five.");
        },
        // You can match ranges
        1..=10 => {
             println!("match: The number is between 1 and 10 (inclusive).");
        }
        _ => { // Wildcard to cover all other cases (match must be exhaustive)
            println!("match: The number is something else.");
        }
    }


    // 5. Exercise: Conditional Logic: if let
    println!("\n--- Exercise 5: if let ---");
    let some_value: Option<i32> = Some(10);
    // let some_value: Option<i32> = None; // Uncomment to test the else case

    if let Some(value) = some_value {
        println!("if let: Got a value from Option: {}", value);
    } else {
        println!("if let: The Option was None.");
    }


    // 6. Exercise: Loops: loop (Infinite Loop)
    println!("\n--- Exercise 6: loop (Infinite Loop) ---");
    let mut counter_loop = 0;
    loop {
        counter_loop += 1;
        println!("loop: Counter is {}", counter_loop);
        if counter_loop == 5 {
            break; // Exit the loop
        }
    }
    println!("loop: Exited loop.");


    // 7. Exercise: Loops: while (Conditional Loop)
    println!("\n--- Exercise 7: while (Conditional Loop) ---");
    let mut counter_while = 3;
    while counter_while > 0 {
        println!("while: Counter is {}", counter_while);
        counter_while -= 1;
    }
    println!("while: Exited loop.");


    // 8. Exercise: Loops: for (Iterator Loop)
    println!("\n--- Exercise 8: for (Iterator Loop) ---");
    println!("for: Iterating over a range:");
    for i in 1..=5 { // Inclusive range from 1 to 5
        println!("  Number: {}", i);
    }


    // 9. Exercise: Type Casting
    println!("\n--- Exercise 9: Type Casting ---");
    let float_val: f64 = 9.81;
    let large_int: i64 = 300;

    // Cast float to integer (truncates)
    let int_from_float = float_val as i32;
    println!("Casting {} (f64) to i32: {}", float_val, int_from_float); // Expected: 9

    // Cast large integer to smaller integer (truncates/wraps)
    // 300 exceeds the range of i8 (-128 to 127). It will wrap around.
    let small_int_from_large = large_int as i8;
    println!("Casting {} (i64) to i8: {}", large_int, small_int_from_large); // Expected: 44 (300 = 1*256 + 44; 256 is 2^8 for i8 range wrap)


    // 10. Exercise: Closures: Basic Closure
    println!("\n--- Exercise 10: Basic Closure ---");
    let my_closure = || { // Closure taking no arguments
        println!("Hello from basic closure!");
    }; // Semicolon because we are assigning the closure to a variable

    my_closure(); // Call the closure


    // 11. Exercise: Closures: Capturing Environment (Immutable Borrow)
    println!("\n--- Exercise 11: Closure Immutable Capture ---");
    let greeting = String::from("Hi from outer scope");

    // This closure immutably borrows 'greeting'
    let print_greeting = || {
        println!("Closure says: {}", greeting);
    }; // Semicolon for assignment

    print_greeting(); // Call the closure
    print_greeting(); // Can call multiple times as it's an immutable borrow

    println!("Can still use original variable after immutable capture: {}", greeting);


    // 12. Exercise: Closures: Capturing Environment (Mutable Borrow or Ownership)
    println!("\n--- Exercise 12: Closure Mutable Capture/Move ---");
    let mut counter = 0;

    // This closure mutably borrows 'counter'. It must be 'mut' because it modifies captured 'counter'.
    // If we used 'move', it would take ownership.
    let mut increment_counter = || { // Needs 'mut' if modifying captured variables by mutable reference
        counter += 1; // Modify captured 'counter'
        println!("Closure incremented counter to: {}", counter);
    }; // Semicolon for assignment

    increment_counter(); // Call the closure
    increment_counter(); // Can call multiple times if FnMut
    increment_counter();

    // Cannot use 'counter' here if 'increment_counter' is still in scope
    // and holds a mutable borrow. In this simple sequential example, the borrow
    // might end after the calls.
    // println!("Original counter after mutable capture: {}", counter); // Might be error if borrow active

    // Alternative using 'move' to take ownership (FnOnce or Fn)
    let mut score = 100;
    // This closure takes ownership of 'score'
    let print_score_once = move || { // 'move' keyword
        println!("Closure using owned score: {}", score); // Uses the moved 'score'
        // score += 1; // Can modify IF the closure type allows it (FnMut or FnOnce),
        //            // and it's marked as mut
    }; // Semicolon for assignment

    print_score_once(); // Call the closure

    // println!("Original score after move capture: {}", score); // Error: value borrowed here after move

    println!("\n--- Lab Complete ---");
}

// No external function definitions needed for these exercises.

```

**Check Questions (To Test Understanding)**

1.  In Rust, what is the value of the expression `{ let x = 5; x * 2 }`?
2.  What is the result of the expression `false || true && false` due to operator precedence?
3.  When would you typically choose a `match` expression over an `if/else if/else` chain?
4.  Explain the difference between a `while` loop and a `for` loop in terms of what controls the loop's execution.
5.  If you cast a large positive `i32` value (like 1000) to an `i8`, what happens to the value, and why?
6.  When defining a closure, what is the difference between capturing a variable by immutable reference, mutable reference, and by value (using the `move` keyword)?

**Detailed Answers to Check Questions**

1.  The value of the expression `{ let x = 5; x * 2 }` is `10`. The block evaluates to the value of its last expression, which is `x * 2`, evaluating to `5 * 2 = 10`.
2.  The result is `false`. Logical operators have precedence: `&&` has higher precedence than `||`. So, `true && false` is evaluated first, resulting in `false`. Then, `false || false` is evaluated, resulting in `false`.
3.  You typically choose a `match` expression over an `if/else if/else` chain when you are checking a single variable against multiple *distinct possible values* or *patterns*, especially when the compiler can help ensure you have covered *all* possible cases (exhaustiveness). `if/else` is better for checking a series of arbitrary boolean conditions.
4.  A `while` loop's execution is controlled by a boolean condition evaluated before each iteration; the loop continues as long as the condition is `true`. A `for` loop's execution is controlled by iterating over the items provided by an **iterator**; the loop runs once for each item the iterator yields until the iterator is exhausted.
5.  Casting a large positive `i32` value (like 1000) to an `i8` will result in **truncation or wrapping**. An `i8` can only hold values from -128 to 127. The value 1000 exceeds this range. When casting between integer types of different sizes, Rust performs a saturating conversion (wrapping). The binary representation of 1000 is truncated to fit into 8 bits, and the resulting value wraps around the range of `i8`. (1000 in binary is 0b1111101000. The last 8 bits are 0b101000. This is 64 + 16 + 8 = 88 in decimal. So `1000 as i8` is 88). The reason is that the `as` keyword performs a simple bit pattern conversion based on the target type's size, not a range check that would prevent overflow.
6.  When defining a closure:
    *   **Immutable capture (`&T`)**: The closure takes a shared reference to the variable. It can read the variable but not modify it. The original variable can still be used immutably after the closure is defined/called.
    *   **Mutable capture (`&mut T`)**: The closure takes an exclusive (mutable) reference to the variable. It can read and modify the variable. The variable must be mutable (`let mut`). While the closure holds the mutable borrow, no other borrows (mutable or immutable) of that variable are allowed. The original variable cannot be used while the mutable borrow is active.
    *   **Capture by value (`move` keyword, `T`)**: The closure takes ownership of the variable's value. The original variable becomes unusable after the closure is defined/called (unless the variable's type is `Copy`). The closure can freely use and potentially modify the captured value (depending on the closure trait: `Fn`, `FnMut`, `FnOnce`).

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025.**
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*