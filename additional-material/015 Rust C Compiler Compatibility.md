# Rust C Compiler Compatibility

Rust can interoperate with C code through FFI (Foreign Function Interface), and when it needs to compile C code (e.g., via `build.rs` using the `cc` crate), it delegates to an actual C compiler installed on the system.

Here’s a summary of commonly used C compilers compatible with Rust on **Windows** and **Linux**, along with usage notes:

___

### **Common C Compilers for Rust (Windows & Linux)**

| Compiler                                                                            | Platform                            | Description                                                                 | Installation Notes                                                                            |
| ----------------------------------------------------------------------------------- | ----------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| **GCC**                                                                             | Linux, Windows (via MinGW or MSYS2) | Widely used GNU C compiler; standard on most Linux distros                  | Linux: `apt install build-essential` / `yum groupinstall "Development Tools"`                 |
| Windows: via [MSYS2](https://www.msys2.org/) or [MinGW-w64](https://mingw-w64.org/) |
| **Clang/LLVM**                                                                      | Linux, Windows                      | Modern, modular C/C++ compiler; compatible with GCC toolchains              | Linux: `apt install clang` / `dnf install clang`                                              |
| Windows: install via LLVM, also available in MSYS2                                  |
| **MSVC (Microsoft C++)**                                                            | Windows only                        | Official Microsoft C compiler; integrates with Visual Studio                | Install [Visual Studio](https://visualstudio.microsoft.com/) with “C++ build tools” component |
| **TinyCC (TCC)**                                                                    | Linux, Windows (limited)            | Very small C compiler; useful for small, quick builds (less common in Rust) | Limited use in Rust projects; not recommended for serious builds                              |

___

### **How Rust Uses C Compilers**

-   **Rust itself doesn't need a C compiler** to compile most projects.
-   But **C compilers are needed** if your project:
    -   Uses `build.rs` scripts with the `cc` crate to compile C code.
    -   Links to native C libraries (via `bindgen`, `cbindgen`, or FFI).
    -   Depends on crates that include C code (e.g., `openssl-sys`, `ring`, etc.).


___

### **How Rust picks the C compiler on each platform**

| Platform                  | Default C Compiler Used                                          |
| ------------------------- | ---------------------------------------------------------------- |
| **Linux**                 | `gcc` (or `clang` if explicitly configured)                      |
| **Windows (MSVC target)** | MSVC compiler (requires Visual Studio Build Tools)               |
| **Windows (GNU target)**  | `gcc` from MinGW-w64 or MSYS2                                    |
| **Cross-compiling**       | Often requires explicitly setting `CC`, `AR`, and target triplet |

___

### **Tips for setup**

-   You can set the compiler explicitly using environment variables:

    ```bash
    export CC=gcc
    export CXX=g++
    ```

-   On Windows with Rust MSVC toolchain: use **MSVC compiler**.
-   On Windows with Rust GNU toolchain: use **MinGW-w64** (install via MSYS2 for best results).
-   

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*