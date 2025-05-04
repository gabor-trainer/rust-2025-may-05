# Lab 02: "Hello, World!" Program with VS Code Debugging

## 2. Introduction & Goal

Welcome to Rust! The traditional first step in learning any new language is printing "Hello, world!". This lab guides you through creating that simple program, but more importantly, it shows you how to set up Visual Studio Code (VS Code) for a smooth Rust development experience, including using its powerful integrated debugger. Debugging is a crucial skill for understanding how your code executes and for finding issues efficiently.

**Primary Learning Objective:** To set up a basic Rust development environment in VS Code and learn the fundamental workflow of writing, running, and debugging a simple Rust program using integrated tools.

**Concrete Outcomes:** By the end of this lab, you will be able to:

*   Set up VS Code with recommended extensions for Rust development (`rust-analyzer`, a debugger).
*   Create a new Rust project using `cargo`.
*   Compile and run a basic Rust program from the VS Code integrated terminal.
*   Set breakpoints in your code within the VS Code editor.
*   Launch the VS Code debugger for your Rust program.
*   Step through your code line-by-line using the debugger controls.

## 3. Scenario

Imagine you are starting a brand new Rust project. Before diving into complex logic or integrating multiple libraries, you need to ensure your fundamental development tools are configured and working correctly. Can you reliably compile your code? Can you execute it? Crucially, can you examine the program's state and execution flow step-by-step? Running a simple "Hello, World!" and stepping through it with a debugger is an excellent way to confirm your integrated development environment setup is solid and ready for more involved tasks.

*Why use VS Code and Rust together?* VS Code is a widely-used, free, and powerful cross-platform code editor. When paired with the `rust-analyzer` extension, it offers sophisticated language support tailored for Rust, such as precise code completion, real-time error and warning highlighting, robust code navigation (like jumping to definitions), and useful refactoring suggestions. Integrating a debugger extension (like `CodeLLDB`) allows you to directly inspect your program's state (variables, call stack) as it runs, a capability far more powerful for diagnosing issues than relying solely on print statements.

## 4. Prerequisites

*   **Software:**
    *   **Rust Toolchain:** Installed (includes `rustc` compiler and `cargo` build tool/package manager). This lab is based on Rust `edition = "2024"`. We assume the following tool versions: `rustc 1.86.0 (05f9846f8 2025-03-31)` and `cargo 1.86.0 (adf9b6ad1 2025-02-28)`. While newer versions should be compatible, these were used for testing. Ensure you have at least Rust 1.56.0 or later, which is required for the 2021 edition features (and thus 2024).
    *   **Visual Studio Code:** Installed and functional.
    *   **Internet Connection:** Required to download and install VS Code extensions.
*   **Knowledge:**
    *   Basic computer literacy, including navigating file systems and opening applications.
    *   Familiarity with opening and using a terminal or command prompt on your operating system.
    *   Understanding of how to find and install extensions within the VS Code interface.
    *   No prior Rust programming knowledge is strictly required for this introductory lab.
*   **Setup Verification:** Before starting the lab steps, open your standard terminal or command prompt (not necessarily the one integrated into VS Code yet) and run these commands to confirm your Rust installation is recognized:
    ```bash
    rustc --version
    cargo --version
    ```
    You should see output indicating version numbers for both. Confirm that VS Code is also installed and launches correctly. If your `rustc` or `cargo` versions are significantly older than the ones mentioned above, consider updating your toolchain using `rustup update`. If Rust is not found at all, refer to the official installation guide: [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install).

## 5. Setup Instructions

1.  **Navigate to your repository and branch:** Before creating the project, ensure you are in your training repository's root directory and that you have switched to the appropriate Git branch for this lab. Check for any uncommitted changes from previous work that you may want to save or discard before creating a new project directory.
2.  **Create a New Rust Project:**
    Open your terminal or command prompt, navigate to the directory within your repository where you want to create this lab's project (e.g., a `labs` or `exercises` subfolder), and run:
    ```bash
    cargo new hello_vscode --edition 2024
    ```
    This command uses `cargo` to create a new directory named `hello_vscode` containing a basic executable Rust project structure. The `--edition 2024` flag ensures the project is configured to use the latest stable Rust edition. Rust editions (2015, 2018, 2021, 2024) are a mechanism for evolving the language in a backwards-compatible way.
3.  **Remove the generated Git repository:** `cargo new` initializes a Git repository by default. Since you are working within an existing training monorepo, this nested `.git` subfolder is redundant and can cause confusion or issues with your main repository's tracking. It is best practice to remove it in a monorepo setup.
    ```bash
    # On Linux/macOS/Git Bash
    rm -rf hello_vscode/.git

    # On Windows Command Prompt
    rmdir /s /q hello_vscode\.git

    # On Windows PowerShell
    Remove-Item -Recurse -Force hello_vscode\.git
    ```
4.  **Open the Project in VS Code:**
    *   Navigate into the newly created project directory using your terminal:
        ```bash
        cd hello_vscode
        ```
    *   Open this directory in VS Code. A convenient way to do this from the terminal (if VS Code is in your system's PATH) is:
        ```bash
        code .
        ```
        (The `.` refers to the current directory). Alternatively, open VS Code manually and use the menu `File > Open Folder...` to select the `hello_vscode` directory.
    *   If VS Code prompts you about trusting the authors of the files in this folder, select "Yes, I trust the authors".
5.  **Install Recommended VS Code Extensions:**
    *   In VS Code, go to the Extensions view. You can open it by clicking the square icon on the left sidebar or by pressing the shortcut `Ctrl+Shift+X` (or `Cmd+Shift+X` on macOS).
    *   In the search bar, type `rust-analyzer`. Select the official `rust-analyzer` extension and click the "Install" button. This extension provides core language features like autocompletion, error checking, hinting, etc.
    *   Next, search for a debugger extension. The widely recommended, cross-platform debugger for Rust in VS Code is `CodeLLDB`. Install it. (If you are on Windows, the `C/C++` extension from Microsoft can also provide debugging capabilities, often used alongside `rust-analyzer`, but `CodeLLDB` is generally considered more tailored for Rust). For the remainder of this lab, we will assume you are using `CodeLLDB`.
    *   After installing the extensions, you may need to restart VS Code (`Developer: Reload Window` from the Command Palette, accessed via `Ctrl+Shift+P`/`Cmd+Shift+P`) or wait a moment for `rust-analyzer` to finish its initial setup (downloading components and analyzing your project). You can often see its status in the bottom bar of the VS Code window.
6.  **Commit often:** As you work through this lab and future exercises, remember to commit your changes frequently using `git commit`. This saves your progress and creates useful checkpoints. Using a structured approach like [Conventional Commits](https://www.conventionalcommits.org/) (e.g., `feat(lab02): initial cargo project setup`, `feat(lab02): added breakpoint and debugged`) can help keep your commit history clean and informative.

## 6. Lab Steps (Iterative Development)

*   **Step 1: Examine the Initial Code**
    *   **Task:** Look at the simple "Hello, world!" program that `cargo` automatically generated when you created the project.
    *   **Code Implementation:** Open the `src/main.rs` file in the VS Code editor (if it's not already open). It should contain the following code:
        ```rust
        fn main() {
            println!("Hello, world!");
        }
        ```
    *   **Explanation:**
        *   `fn main()`: This defines the `main` function, which is the required entry point for any executable Rust program. When your compiled program starts, execution begins within the body (`{}`) of this function. `fn` is the keyword used to declare a function. `()` signifies that `main` takes no arguments.
        *   `println!("Hello, world!")`: This line calls the `println!` macro to print the string literal `"Hello, world!"` to the standard output (your terminal or console), followed by a newline character. The exclamation mark `!` is how you distinguish a macro call from a regular function call in Rust.
        *   Observe how the `rust-analyzer` extension provides syntax highlighting, potentially shows helpful hints or warnings, and allows you to hover over `println!` to see its documentation.
    *   **Verification:** Simply read and understand the structure and purpose of this small program. Confirm that `rust-analyzer` appears to be active (e.g., syntax highlighting looks correct, no major error icons in the bottom bar).

*   **Step 2: Running the Program (Integrated Terminal)**
    *   **Task:** Compile and run your program using the `cargo run` command directly within VS Code's integrated terminal.
    *   **Code Implementation:**
        *   Open the integrated terminal in VS Code. You can do this via the menu `Terminal > New Terminal` or by pressing the shortcut `Ctrl+` \` (backtick).
        *   A terminal panel will appear, usually at the bottom of the VS Code window. Ensure its current working directory is your `hello_vscode` project root (`cd hello_vscode` if needed).
        *   In the terminal, type the following command and press Enter:
        ```bash
        cargo run
        ```
    *   **Explanation:**
        *   `cargo run`: This command is a shortcut. First, it tells Cargo to build your project (compile the source code). If you haven't built since the last change, `cargo` will invoke `rustc` to compile `src/main.rs` into an executable binary, typically placed in the `target/debug/` directory. You will see compilation messages. Second, after a successful build, `cargo run` immediately executes the compiled binary.
    *   **Verification:** You should see output in the integrated terminal similar to this:
        ```text
           Compiling hello_vscode v0.1.0 (/path/to/your/project/hello_vscode)
            Finished dev [unoptimized + debuginfo] target(s) in X.Ys
             Running `target/debug/hello_vscode`
        Hello, world!
        ```
        The crucial part is the last line, "Hello, world!", which is the output of your running program. This verifies that your Rust installation is working correctly with Cargo and that you can build and run from within VS Code.

*   **Step 3: Setting a Breakpoint**
    *   **Task:** Instruct the debugger to pause execution at a specific line of code.
    *   **Code Implementation:** In the `src/main.rs` editor window, move your mouse cursor into the area directly to the left of the line number `2` (the line containing `println!("Hello, world!");`). Click your mouse in this "gutter" area.
    *   **Explanation:** A solid red dot should appear in the gutter next to the line number. This red dot signifies a **breakpoint**. When you run your program under the control of the debugger, execution will automatically stop *just before* this specific line of code is executed. Breakpoints are fundamental for inspecting the state of your program at a particular moment.
    *   **Verification:** Ensure that the red dot is clearly visible next to the line number where you clicked.

*   **Step 4: Starting the Debugger**
    *   **Task:** Launch your program with the debugger attached, allowing you to hit the breakpoint you just set.
    *   **Code Implementation:**
        *   Switch to the "Run and Debug" view in the VS Code sidebar. You can do this by clicking the icon that looks like a bug with a play button (usually the fourth or fifth icon down on the left) or by pressing the shortcut `Ctrl+Shift+D` (or `Cmd+Shift+D`).
        *   In the "Run and Debug" view, click the prominent green "Run and Debug" button.
        *   VS Code may automatically detect your Rust project and suggest a default configuration (often labeled something like "Debug hello_vscode"). Select this option if prompted. If it asks you to choose an environment or create a `launch.json` file, look for a Rust or Cargo option, or ensure your `CodeLLDB` extension is active.
        *   A common shortcut for starting the debugger with the default configuration is simply pressing the `F5` key.
    *   **Explanation:** This action triggers a debug build of your project (similar to `cargo build`), and then starts the resulting executable binary with the `CodeLLDB` debugger attached. The debugger takes control of the program's execution and monitors for breakpoints.
    *   **Verification:**
        *   Your program's execution should pause immediately upon reaching the breakpoint you set. The line with the breakpoint (`println!...`) will be highlighted (often in yellow), and a small yellow arrow will point to it from the left.
        *   A "Debug toolbar" will appear, typically floating near the top center of your VS Code window. This toolbar contains controls for stepping through code (Step Over, Step Into, etc.), continuing execution, restarting, and stopping.
        *   The "Run and Debug" sidebar will populate with panels showing information about the call stack, local variables, and breakpoints. (The variables panel won't show much yet for this simple program).

*   **Step 5: Stepping Through Code**
    *   **Task:** Execute the currently highlighted line of code and advance to the next executable statement.
    *   **Code Implementation:** On the Debug toolbar, locate and click the "Step Over" button. This button typically looks like a curved arrow jumping over a dot, or you can use the shortcut `F10`.
    *   **Explanation:**
        *   The "Step Over" action executes the line of code that is currently highlighted by the debugger. In this case, it executes the `println!("Hello, world!");` macro call.
        *   After executing the line, the debugger pauses execution again at the very next instruction. In this simple `main` function, the next logical step after printing is the end of the function.
        *   **Important:** Output from `println!` during a debugging session typically appears in the **DEBUG CONSOLE** tab within VS Code's lower panel, not in the regular "TERMINAL" tab you used for `cargo run`. Make sure you switch to the "DEBUG CONSOLE" tab to see the output.
    *   **Verification:**
        *   The yellow execution highlight should move past the `println!` line (in this case, likely disappear as `main` finishes).
        *   The text "Hello, world!" should now be visible in the "DEBUG CONSOLE" tab of your integrated panel.

*   **Step 6: Stopping the Debugger**
    *   **Task:** Terminate the current debugging session and stop the running program.
    *   **Code Implementation:** On the Debug toolbar, click the "Stop" button. This button is usually represented by a solid red square.
    *   **Explanation:** Clicking "Stop" immediately ends the debugging session and halts the execution of the program that was being debugged.
    *   **Verification:** The Debug toolbar should disappear, the execution highlight is removed, and you are returned to the standard editing and terminal views. The program is no longer running.

## 7. Running the Application / Testing

You have explored the primary ways to run your Rust application in this lab:

*   **Standard Run:** Use the `cargo run` command in the integrated terminal (`Ctrl+` \`). This compiles (if needed) and runs the executable. Output appears in the "TERMINAL" tab. This is the typical way to run your application for development or testing its final output.
*   **Debug Run:** Set breakpoints in your code, then start the debugger using `F5` or the "Run and Debug" view (Ctrl+Shift+D). This compiles (if needed, with debug symbols enabled) and runs the executable under debugger control. Output from `println!` appears in the "DEBUG CONSOLE" tab. This is essential for step-by-step execution and state inspection.

**Other useful Cargo commands:**

*   `cargo check`: Quickly checks your code for compilation errors and warnings without producing an executable. Use this frequently while coding for rapid feedback.
*   `cargo build`: Compiles your project and creates the executable binary (e.g., in `target/debug/` or `target/release/` if using `--release`) but does not run it. Useful when you just want the executable file.

**(Optional) Testing:** While this simple program doesn't include tests, Rust has robust built-in support for testing using functions marked with `#[test]`. Tests are run using the `cargo test` command. Learning to write tests early is highly recommended for building reliable software.

## 8. Key Concepts Review

*   **`cargo new`**: Initializes a new Rust project with a standard layout and a `Cargo.toml` manifest file.
*   **`Cargo.toml`**: The project manifest file, containing metadata, configuration, and dependencies.
*   **`src/main.rs`**: The conventional location for the main source code file for an executable (binary) crate.
*   **`fn main()`**: The mandatory entry point function for any executable Rust program.
*   **`println!`**: A standard library macro used for printing formatted text to standard output, followed by a newline.
*   **VS Code Extensions:**
    *   `rust-analyzer`: Provides core IDE language services for Rust (syntax highlighting, error checking, completion, navigation).
    *   `CodeLLDB` (or similar): Enables debugging functionality within VS Code for Rust programs.
*   **Breakpoint:** A deliberate pause point in the code that the debugger respects, allowing you to inspect the program's state at that moment. Set by clicking in the editor's gutter.
*   **Debugging Session:** The active state of running a program under the control of a debugger (initiated via F5 or the "Run and Debug" view).
*   **Debug Toolbar:** The set of controls within VS Code used to interact with the debugger (e.g., Step Over, Continue, Stop).
*   **Step Over (F10):** A debugger command that executes the current line of code and pauses at the next line within the same function (or the function's caller if the current line is the last).
*   **DEBUG CONSOLE:** A dedicated panel in VS Code where output from the program being debugged (like `println!`) typically appears.

## 9. (Optional) Challenges / Next Steps

1.  **Multiple Breakpoints:** Add a second line `println!("Learning Rust with VS Code!");` in `main.rs`. Set breakpoints on *both* `println!` lines. Start the debugger (F5) and use "Step Over" (F10) twice to step through each line sequentially, observing the output in the Debug Console after each step.
2.  **Inspect a Variable:** Modify your `main.rs` to introduce a variable and print it:
    ```rust
    fn main() {
        let debug_message = "Debugging in VS Code is helpful!"; // Add this line
        println!("{}", debug_message);                         // Modify this line
    }
    ```
    Set a breakpoint on the `println!` line. Start debugging (F5). When execution pauses on the `println!` line, look at the "VARIABLES" section in the "Run and Debug" sidebar. You should see the `debug_message` variable listed along with its current value. Use "Step Over" (F10) to execute the print statement.
3.  **The Rust Book:** Begin reading "The Rust Programming Language" book ([https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)), starting with Chapters 1 ("Getting Started") and 2 ("Programming a Guessing Game") to build upon the basics.
4.  **VS Code Documentation:** Explore the official VS Code documentation specifically for Rust support: [https://code.visualstudio.com/docs/languages/rust](https://code.visualstudio.com/docs/languages/rust).

## 10. (Optional) Troubleshooting

*   **`rust-analyzer` not providing language features:**
    *   Verify that the `rust-analyzer` extension is installed and enabled in the Extensions view (`Ctrl+Shift+X`).
    *   Open the VS Code Command Palette (`Ctrl+Shift+P` or `Cmd+Shift+P`) and search for `rust-analyzer: Status` to check its internal state and logs for errors.
    *   Ensure your fundamental Rust installation (`rustc --version`, `cargo --version`) is working outside of VS Code.
    *   Sometimes, simply restarting VS Code (`Developer: Reload Window` from Command Palette or closing/reopening) can resolve temporary issues.
*   **Debugger fails to start, errors about launch configuration, or `CodeLLDB` issues:**
    *   Confirm that `CodeLLDB` (or your chosen debugger extension) is installed and enabled.
    *   Ensure your project builds successfully using `cargo build` in the terminal first. Debuggers require a compilable executable.
    *   If VS Code prompts you to create a `launch.json` file or select a configuration, look for options related to Rust or Cargo binaries. The `rust-analyzer` extension often helps generate default configurations. You can manually access this via the gear icon in the "Run and Debug" view.
    *   Ensure necessary system debugger components (like LLDB itself) are available on your system if `CodeLLDB` requires them separately (though often bundled or handled by `rustup`).
*   **Breakpoint is not being hit:**
    *   Is the red dot clearly placed on an executable line of code? Breakpoints cannot be set on comments, blank lines, function signatures, or declarations that don't translate to executable instructions.
    *   Did you start the program using the debugger (F5 or the "Run and Debug" view), or did you just use `cargo run`? Breakpoints are only active when the debugger is attached.
    *   Is the program's execution flow actually reaching the line where the breakpoint is set? (For "Hello, world!", it always does, but for more complex programs, conditional logic might prevent reaching a breakpoint).

## 11. Conclusion

Well done! You have successfully set up VS Code for Rust development, created your first Rust project using `cargo`, run it from the integrated terminal, and most importantly, used the integrated debugger to pause execution and step through your code. While "Hello, world!" is a very simple program, the workflow you've practiced here – writing code, running it, and debugging it – is absolutely fundamental to developing Rust applications effectively using a modern IDE like VS Code. Mastering the debugger will be invaluable as you tackle more complex challenges. Keep exploring and building!

Remember to commit the final state of your lab work to your repository using `git commit` and push your changes using `git push` to synchronize them. Following Conventional Commits guidelines will make your project history easy to follow.

## 12. Final Solution Code

Here is the complete code for `src/main.rs` as it should be after completing the core steps of the lab (before attempting the optional challenges):

```rust
// Final Code for src/main.rs
fn main() {
    println!("Hello, world!");
}
```

## 13. Check Questions (To Test Understanding)

1.  What is the primary role of the `rust-analyzer` extension within VS Code for Rust development?
2.  Which `cargo` command would you use in the VS Code integrated terminal to compile your program (if necessary) and run it directly, *without* activating the debugger?
3.  How do you visually set a breakpoint in the VS Code editor's gutter, and what is the effect of setting one when you then start a debug session?
4.  When running your program under the debugger (e.g., launched with F5), where does the text output generated by a `println!` macro typically appear within the VS Code interface?
5.  If the debugger is paused on a line of code, what action is performed when you use the "Step Over" command (often bound to the F10 key)?

## 14. Detailed Answers to Check Questions

1.  **`rust-analyzer` role:** The main purpose of the `rust-analyzer` extension is to provide comprehensive **language intelligence** for Rust inside VS Code. This includes crucial features like real-time diagnostics (syntax errors, warnings), intelligent code completion, code formatting assistance, navigation (like "Go to Definition"), and refactoring tools. It significantly enhances the developer experience by making coding in Rust much more efficient and less error-prone within the editor.
2.  **Compile and Run command:** The command used in the integrated terminal to compile (if source files have changed) and immediately run your Rust program without involving the debugger is **`cargo run`**.
3.  **Setting a breakpoint:** You set a breakpoint visually by **clicking in the gutter area** (the space to the left of the line numbers) next to the specific line of code you want to pause on. A red dot appears there. When you subsequently start your program using the debugger, execution will automatically **pause** just before the code on that line is executed, allowing you to inspect variables and the call stack.
4.  **`println!` output during debug:** During a debugging session started via F5 or the "Run and Debug" view, the output generated by `println!` calls typically appears in the **DEBUG CONSOLE** tab within the integrated panel at the bottom of VS Code.
5.  **"Step Over" (F10) action:** When paused at a breakpoint or after a previous step, the "Step Over" action executes the single line of code currently highlighted by the debugger. If that line contains a function call, "Step Over" executes the *entire* function without pausing inside it. After executing the line, the debugger then pauses again on the *next* executable line in the current scope. It allows you to advance through the code one statement at a time without diving into function implementations unless you explicitly choose to ("Step Into").

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*