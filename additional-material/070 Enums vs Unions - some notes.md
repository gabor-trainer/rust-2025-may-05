# Enums vs Unions: some notes 


Rust enums represent a value that can be *one of several possible variants*. Internally, an enum value needs to store two pieces of information:

1.  **The Discriminant:** Which variant the value actually is.
2.  **The Data Payload (if any):** The data associated with that specific variant.

The way these are stored varies depending on whether the enum has associated data.

**1. Simple Enums (Without Associated Data)**

*   **Example:**
    ```rust
    enum TrafficLight { Red, Yellow, Green }
    enum Command { Undo, Redo, Save } // Default discriminants: 0, 1, 2
    enum HttpCode { Ok = 200, NotFound = 404, InternalError = 500 } // Explicit discriminants
    ```
*   **Internal Representation:** Each variant is assigned a unique integer **discriminant**. By default, Rust assigns discriminants starting from 0, but you can set them explicitly.
*   **Size of the Discriminant Integer:** The size of the memory used to store the discriminant is determined by the **smallest unsigned integer type** (`u8`, `u16`, `u32`, `u64`) that can represent the largest possible discriminant value assigned to any variant.
    *   For `TrafficLight` (3 variants, discriminants 0, 1, 2), the largest discriminant is 2. A `u8` can hold this. The size is typically 1 byte.
    *   For `HttpCode` (largest discriminant 500), a `u8` (max 255) is not enough. A `u16` (max 65535) is sufficient. The size is typically 2 bytes.
*   **Memory Layout:** A value of a simple enum is stored directly as this small discriminant integer.

**2. Enums with Associated Data**

*   **Example:**
    ```rust
    enum Asset {
        Stock { symbol: String, shares: u64 }, // Struct data
        Crypto(String, f64),                  // Tuple data
        Bond(u32),                            // Single field data
        Cash,                                 // No data
    }
    
    enum MaybeNumber { Some(i32), None } // Similar to Option
    ```
*   **Internal Representation (You can think like a C/C++ Tagged Union):** Conceptually, these enums are represented as a you would creatively implemented it a C/C++ **tagged union** idiom. The memory layout includes:
    *   A field for the **discriminant** (like in simple enums, usually an appropriately sized unsigned integer).
    *   Enough space (like a union) to hold the data payload for the **largest data variant**.


What this means is that the enum will take up the size of the largest variant, plus the size of the discriminant, (or even less :-)) For example:

*   Simple enums store just a discriminant, sized as the minimal unsigned integer needed.
*   Enums with data naively use a discriminant tag + space for the largest payload.
*   A compiler technique that leverages invalid bit patterns (niches) in data types (especially pointers) to store the discriminant of data-less variants, often reducing the enum size to just the size of the largest data variant.
*   

## Union vs Enum

1.  **`enum` (Idiomatic & Safe can not be used or misused like union)**
    *   This is what you've been learning about and using. Terminulogy: Variants, discriminant and Value.
    *   It represents a value that can be **one of several variants**, but only one at a time.
    *   It explicitly stores a **discriminant** to tell you *which* variant is currently active.
    *   Accessing the associated data of a variant is done safely via pattern matching (`match`, `if let`), which ensures you only access the data of the current variant.
    *   The compiler handles the memory layout, often optimizing it so that the memory is laid out similarly to a union but this access is excluvely for the compiler .
    *   This is the standard, safe, and idiomatic way to represent distinct possibilities in Rust.

2.  **`union` (Unsafe & Low-Level)**
    *   This is a distinct type in Rust using the `union` keyword.
    *   It is similar to a `union` in C or C++. It represents a memory location that can hold a value of **one of several types** defined within the union.
    *   Crucially, unlike `enum`, a `union` in Rust **does NOT store a discriminant** to tell you which type is currently active in that memory location.
    *   Accessing a field (member) of a `union` is inherently **`unsafe`** in Rust. You must wrap access in an `unsafe` block because the compiler cannot guarantee that the data in memory is actually of the type you are trying to read it as, and reading the wrong type can lead to undefined behavior.
    *   Fields in a `union` generally cannot have types that need to be dropped (`Drop` implementation), unless the union itself implements `Drop` (which is complex). This prevents issues with implicitly dropping the wrong type.
    *   Its primary use cases are **Foreign Function Interface (FFI)** to interact with C/C++ code that uses unions, or very specific low-level programming scenarios where overlapping memory layouts are strictly necessary.

**In Summary:**

*   **`enum`**: **Safe**, idiomatic, way for representing distinct possibilities with compile-time guaranteed variant information. Use this for most scenarios.
*   **`union`**: **Unsafe**, low-level, union for representing overlapping memory layouts, primarily for FFI  or specific low-level programming scenarios. Use this only when absolutely necessary and with caution.

So, while the `union` keyword exists for specific unsafe use cases, representing distinct variants or possibilities in a safe, high-level way, they way is `enum`. (This is the way :-) as the Mandalorian would say.

[Additional reading: https://en.wikipedia.org/wiki/Tagged_union](https://en.wikipedia.org/wiki/Tagged_union)