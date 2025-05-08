# Lab 110: Operator overloading C++ vs Rust
1.  **Mechanism:**
    *   **C++:** Operator overloading is typically achieved by defining **special member functions** within a class or as **free functions** (non-member functions). The operator symbol followed by `operator` becomes the function name (e.g., `operator+`, `operator[]`).
        ```c++
        // C++ example
        struct Vector {
            int x, y;
            Vector operator+(const Vector& other) const { // Member function overload
                return {x + other.x, y + other.y};
            }
        };
        ```
    *   **Rust:** Operator overloading is done by **implementing specific traits** defined in the standard library, primarily in the `std::ops` module (for arithmetic, bitwise, indexing) and `std::cmp` (for comparison).
        ```rust
        // Rust example
        use std::ops::Add;

        #[derive(Debug)]
        struct Vector {
            x: i32,
            y: i32,
        }

        impl Add for Vector { // Implementing the Add trait
            type Output = Self;
            fn add(self, other: Self) -> Self { // Defining the add method required by the trait
                Vector { x: self.x + other.x, y: self.y + other.y }
            }
        }
        ```

2.  **Set of Overloadable Operators:**
    *   **C++:** Allows overloading a *wide range* of operators, including arithmetic (`+`, `-`, `*`, `/`, `%`), comparison (`==`, `!=`, `<`, `>`, `<=`, `>=`), logical (`!`, `&&`, `||` - though `&&` and `||` are tricky due to short-circuiting), bitwise (`&`, `|`, `^`, `~`, `<<`, `>>`), assignment (`=`, `+=`, etc.), function call (`()`), subscript (`[]`), member access (`*`, `->`), increment/decrement (`++`, `--`). Some are *not* overloadable (e.g., `.`, `::`, `?:`, `sizeof`).
    *   **Rust:** Allows overloading a *fixed set* of operators defined by standard traits. You cannot invent new operators or overload arbitrary syntax. The available traits cover common operators: arithmetic (`Add`, `Sub`, `Mul`, `Div`, `Rem`), bitwise (`BitAnd`, `BitOr`, `BitXor`, `Shl`, `Shr`), negation (`Neg`), dereferencing (`Deref`, `DerefMut`), indexing (`Index`, `IndexMut`), comparison (`PartialEq`, `Eq`, `PartialOrd`, `Ord`).

3.  **Restrictions and Semantics:**
    *   **C++:** Provides a lot of freedom. You can technically overload an operator like `+` to perform subtraction, or `==` to always return false. This flexibility can lead to code that is confusing or surprising for users. You cannot change operator precedence or associativity. Overloading binary operators requires at least one operand to be a user-defined type.
    *   **Rust:** Tightly coupled to traits, which often imply a certain semantic meaning. While the compiler doesn't strictly *enforce* that `Add` implementations perform addition, implementing `Add` to do subtraction would violate conventions and frustrate users. This trait-based approach encourages predictable behavior. You cannot change operator precedence or associativity. You cannot overload operators for built-in types alone (e.g., you can't change how `i32 + i32` works, but you *can* implement `Add` for a `struct MyInt(i32)`).

4.  **Integration with the Type System:**
    *   **C++:** Operator overloads are just functions with special names. The compiler resolves them based on function overloading rules (matching argument types).
    *   **Rust:** Operator overloading *is* trait implementation. This integrates well with Rust's trait system and generic programming. For example, a function generic over `T: Add` can use the `+` operator on type `T`.

5.  **Safety and Borrowing:**
    *   **C++:** Operator overloads, like any C++ function, can be implemented in unsafe ways (e.g., returning dangling references from `operator[]`).
    *   **Rust:** Operator trait methods operate within the borrow checker's rules. Methods like `Index` and `IndexMut` deal with references and mutable references, ensuring memory safety. For instance, `IndexMut::index_mut` returns a mutable reference, and the borrow checker prevents having multiple mutable references or simultaneous mutable and immutable references to the indexed location.

**Summary of Differences:**

| Feature            | C++ Operator Overloading                  | Rust Operator Overloading                                   |
| :----------------- | :---------------------------------------- | :---------------------------------------------------------- |
| **Mechanism**      | Special member/free functions             | Implementing standard library traits                        |
| **Operators**      | Wide range (most)                         | Fixed set defined by traits                                 |
| **Flexibility**    | High (semantics not enforced)             | Lower (semantics implied by traits)                         |
| **Predictability** | Can be low if abused                      | Generally high due to trait semantics                       |
| **Integration**    | Function overloading                      | Trait system, generic programming                           |
| **Safety**         | Can be unsafe depending on implementation | Integrated with borrow checker (generally safe)             |
| **Syntax**         | `operator+`, `operator[]`, etc.           | `impl Add`, `impl Index`, etc., with `add`, `index` methods |

In essence, C++ provides raw power and flexibility, allowing you to redefine many operators with potentially arbitrary behavior. Rust takes a more constrained approach, using traits to ensure operator overloading is applied consistently and predictably, integrating it smoothly with its type and safety systems, at the cost of being able to overload fewer symbols.


## Operator Overloading Traits


**1. Arithmetic Operators (`std::ops`)**

These traits are for binary arithmetic operations (like `a + b`). They define an `Output` type for the result and an `op` method (like `add`, `sub`, etc.) that takes `self` and `rhs` (right-hand side) arguments.

*   `std::ops::Add`: Overloads the `+` operator. Defines the `add(self, rhs)` method.
*   `std::ops::Sub`: Overloads the `-` operator. Defines the `sub(self, rhs)` method.
*   `std::ops::Mul`: Overloads the `*` operator. Defines the `mul(self, rhs)` method.
*   `std::ops::Div`: Overloads the `/` operator. Defines the `div(self, rhs)` method.
*   `std::ops::Rem`: Overloads the `%` operator. Defines the `rem(self, rhs)` method.

**2. Compound Assignment Operators (`std::ops`)**

These traits are for operations like `a += b`. They define an `assign_op` method (like `add_assign`, `sub_assign`, etc.) that takes `&mut self` and `rhs` arguments.

*   `std::ops::AddAssign`: Overloads the `+=` operator. Defines the `add_assign(&mut self, rhs)` method.
*   `std::ops::SubAssign`: Overloads the `-=` operator. Defines the `sub_assign(&mut self, rhs)` method.
*   `std::ops::MulAssign`: Overloads the `*=` operator. Defines the `mul_assign(&mut self, rhs)` method.
*   `std::ops::DivAssign`: Overloads the `/=` operator. Defines the `div_assign(&mut self, rhs)` method.
*   `std::ops::RemAssign`: Overloads the `%=` operator. Defines the `rem_assign(&mut self, rhs)` method.

**3. Unary Operators (`std::ops`)**

These traits define methods that take `self` (or `&self` or `&mut self`) and return a value.

*   `std::ops::Neg`: Overloads the unary `-` operator (like `-x`). Defines the `neg(self)` method.
*   `std::ops::Not`: Overloads the unary `!` operator (logical/bitwise NOT). Defines the `not(self)` method.

**4. Bitwise Operators (`std::ops`)**

Similar structure to arithmetic operators, defining `Output` and an `op` method.

*   `std::ops::BitAnd`: Overloads the bitwise `&` operator. Defines the `bitand(self, rhs)` method.
*   `std::ops::BitOr`: Overloads the bitwise `|` operator. Defines the `bitor(self, rhs)` method.
*   `std::ops::BitXor`: Overloads the bitwise `^` operator. Defines the `bitxor(self, rhs)` method.
*   `std::ops::Shl`: Overloads the left shift `<<` operator. Defines the `shl(self, rhs)` method.
*   `std::ops::Shr`: Overloads the right shift `>>` operator. Defines the `shr(self, rhs)` method.

**5. Compound Bitwise Assignment Operators (`std::ops`)**

Similar structure to compound arithmetic assignment operators.

*   `std::ops::BitAndAssign`: Overloads the `&=` operator. Defines the `bitand_assign(&mut self, rhs)` method.
*   `std::ops::BitOrAssign`: Overloads the `|=` operator. Defines the `bitor_assign(&mut self, rhs)` method.
*   `std::ops::BitXorAssign`: Overloads the `^=` operator. Defines the `bitxor_assign(&mut self, rhs)` method.
*   `std::ops::ShlAssign`: Overloads the `<<=` operator. Defines the `shl_assign(&mut self, rhs)` method.
*   `std::ops::ShrAssign`: Overloads the `>>=` operator. Defines the `shr_assign(&mut self, rhs)` method.

**6. Indexing Operators (`std::ops`)**

These traits allow using the square bracket `[]` syntax. They define an `Output` associated type which is the type of the element being indexed, and an `index` method.

*   `std::ops::Index`: Overloads immutable indexing (`container[index]`). Defines the `index(&self, index)` method, returning a `&Self::Output`.
*   `std::ops::IndexMut`: Overloads mutable indexing (`container[index] = value`). Defines the `index_mut(&mut self, index)` method, returning a `&mut Self::Output`.

**7. Dereferencing Operators (`std::ops`)**

These traits are primarily used for smart pointers to allow them to behave like references, but they technically overload the unary `*` operator.

*   `std::ops::Deref`: Overloads the `*` operator for immutable dereferencing (like `*my_box`). Defines the `deref(&self)` method, returning a `&Self::Target`.
*   `std::ops::DerefMut`: Overloads the `*` operator for mutable dereferencing (like `*my_box = value`). Defines the `deref_mut(&mut self)` method, returning a `&mut Self::Target`.

**8. Comparison Operators (`std::cmp`)**

These traits are implemented to allow using comparison operators (`==`, `!=`, `<`, `>`, `<=`, `>=`). They do *not* use the `std::ops` module.

*   `std::cmp::PartialEq`: Overloads `==` and `!=`. Defines the `eq(&self, other: &Rhs)` and `ne(&self, other: &Rhs)` methods. Often derived automatically (`#[derive(PartialEq)]`).
*   `std::cmp::Eq`: Indicates *total* equality (if `a == b` and `b == c`, then `a == c`). This is a marker trait with no methods; it requires `PartialEq` to also be implemented. Often derived automatically (`#[derive(Eq)]`).
*   `std::cmp::PartialOrd`: Overloads `<`, `>`, `<=`, and `>=`. Defines the `partial_cmp(&self, other: &Rhs)` method (returns `Option<Ordering>`) and default implementations for the other comparison methods based on `partial_cmp`. Often derived automatically (`#[derive(PartialOrd)]`).
*   `std::cmp::Ord`: Indicates *total* ordering. Defines the `cmp(&self, other: &Rhs)` method (returns `Ordering`) and default implementations for `PartialOrd` and `PartialEq`. Requires `PartialOrd` and `Eq`. Often derived automatically (`#[derive(Ord)]`).

## PartialEq vs Eq

Both traits are used to implement equality comparisons (`==` and `!=`), but they represent different mathematical properties of equality:

1.  **`std::cmp::PartialEq`** (Partial Equivalence)
    *   **Purpose:** Implements the comparison operators `==` and `!=`.
    *   **Requirement:** You **must** implement `PartialEq` to use `==` and `!=` on your type.
    *   **Property:** Guarantees **partial equivalence**. This means:
        *   **Reflexivity:** For any `a`, `a == a` is *potentially* true (or sometimes false, especially with floating-point numbers or types that can represent "not a number").
        *   **Symmetry:** For any `a` and `b`, if `a == b` is true, then `b == a` is true.
        *   **Transitivity is NOT guaranteed:** You *cannot* assume that if `a == b` is true and `b == c` is true, then `a == c` is also true.
    *   **Methods:** Requires implementing the `eq(&self, other: &Rhs)` method. The `ne(&self, other: &Rhs)` method has a default implementation based on `eq`.
    *   **Example Use Cases:**
        *   **Floating-point numbers (`f32`, `f64`):** IEEE 754 standard specifies that `NaN` (Not a Number) is *not equal to anything*, including itself. So, `f32::NAN == f32::NAN` evaluates to `false`. This violates the reflexivity property required for total equality (`Eq`), so floating-point types only implement `PartialEq`.
        *   **Types that can represent ambiguous or invalid states:** If a type can be in a state where equality to itself or other values is ill-defined, it might only implement `PartialEq`.

2.  **`std::cmp::Eq`** (Total Equivalence)
    *   **Purpose:** Represents **total equivalence**.
    *   **Requirement:** `Eq` is a **marker trait**. It has **no methods** of its own. It *only* indicates that the type *also* implements `PartialEq` and satisfies the stricter properties of total equivalence. You **must** implement `PartialEq` before you can implement `Eq`.
    *   **Property:** Guarantees **total equivalence**. This means:
        *   **Reflexivity:** For any `a`, `a == a` is *always* true.
        *   **Symmetry:** For any `a` and `b`, if `a == b` is true, then `b == a` is true.
        *   **Transitivity:** For any `a`, `b`, and `c`, if `a == b` is true and `b == c` is true, then `a == c` is *always* true.
    *   **Methods:** None.
    *   **Example Use Cases:**
        *   Most fundamental data types (`i32`, `bool`, `char`, `String`, `Vec`, tuples of `Eq` types): For these types, if two values hold the same data, they are truly equivalent in all contexts.
        *   Types that are used as keys in hash maps (`HashMap`) or elements in sets (`HashSet`): These data structures often rely on the `Eq` trait (along with `Hash`) to ensure that equal keys/elements behave consistently (e.g., two keys that compare as equal should always hash to the same value).

**In Simple Terms:**

*   `PartialEq` means "I know how to check if two values are equal or not, but there might be cases where a value isn't equal to itself (like `NaN`) or where equality isn't transitive."
*   `Eq` means "I know how to check for equality, and I guarantee that equality is always reflexive (`a == a` is true) and transitive (if `a == b` and `b == c`, then `a == c`). My type represents total equivalence."

**Deriving `PartialEq` and `Eq`:**

You can often use `#[derive(PartialEq)]` and `#[derive(Eq)]` on structs and enums.

*   `#[derive(PartialEq)]`: Can be derived as long as all the fields/variants of the type implement `PartialEq`.
*   `#[derive(Eq)]`: Can *only* be derived if `#[derive(PartialEq)]` is also present *and* all the fields/variants of the type implement `Eq`. This ensures that if you derive `Eq`, the transitivity and reflexivity properties hold for your type based on the properties of its components.

Understanding the distinction is important because some contexts (like using a type as a `HashMap` key) require the stronger guarantee of `Eq`, while others just need `PartialEq`.

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*