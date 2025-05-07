# Iterating over a vector by Value vs. By Reference

The core difference is how the `for` loop interacts with the `composers` vector:

*   **by reference: (`for composer in &composers`)**:
    *   The `for` loop is iterating over a **shared reference** to the `composers` vector (`&composers`).
    *   When you iterate over `&Vec<T>`, the `for` loop implicitly calls the `IntoIterator` implementation for `&Vec<T>`. This specific `IntoIterator` implementation returns an iterator that yields **shared references (`&T`)** to the elements within the vector.
    *   Inside the loop, the `composer` variable in each iteration is of type `&Person`.
    *   Because the loop only takes a reference to the vector, the original `composers` vector **remains valid and usable** after the loop finishes. No ownership of the `Person` structs or the vector's buffer is transferred out of the vector by the loop.
    *   Memory usage: The `Person` structs and their internal `String` data stay in the vector's allocated memory. The loop simply borrows references to them.

*   **by value (`for composer in composers`)**:
    *   The `for` loop is iterating directly over the `composers` vector **by value**.
    *   When you iterate over `Vec<T>` directly, the `for` loop implicitly calls the `IntoIterator` implementation for `Vec<T>`. This `IntoIterator` implementation **consumes** the vector and returns an iterator that yields the elements **by value (`T`)**.
    *   Inside the loop, the `composer` variable in each iteration is of type `Person`.
    *   Since `Person` contains a `String` (which is not `Copy`), yielding `Person` by value means the ownership of each `Person` struct (including its `String`) is **moved** out of the vector and into the `composer` variable for that iteration.
    *   As the loop progresses and each `composer` goes out of scope at the end of its iteration, the `Drop` implementation for `Person` (which drops the `String`) is called, deallocating the memory used by that individual `String`.
    *   Once the loop finishes, the original `composers` variable is **no longer valid or usable** because the vector itself was moved into the loop mechanism and consumed.
    *   Memory usage: The memory for each `Person` struct and its `String` is effectively moved out of the vector and then deallocated piece by piece as the loop proceeds and each `composer` variable is dropped. The vector's main data buffer is also deallocated when the consumed vector value goes out of scope after the loop.

In essence:
*   `for item in &collection` = iterate over **references**, collection is **not consumed**.
*   `for item in collection` = iterate over **values**, collection **is consumed**.

**2) Rewriting the `for` loop using explicit `Iterator`**

We will rewrite both examples using the explicit `Iterator` and `while let` structure to match the memory usage and behavior of the originals.

**Snippet 1 Refactored (Equivalent to `for composer in &composers`)**

This uses the `iter()` method, which is the explicit way to get an iterator that yields shared references (`&T`).

```rust
struct Person {
    name: String,
    birth: i32,
}

fn main() {
    let mut composers = Vec::new();
    composers.push(Person {
        name: "Palestrina".to_string(),
        birth: 1525,
    });
    composers.push(Person {
        name: "Dowland".to_string(),

        birth: 1563,
    });
    composers.push(Person {
        name: "Lully".to_string(),
        birth: 1632,
    });

    // Refactored loop equivalent to `for composer in &composers`
    // `composers.iter()` is shorthand for `(&composers).into_iter()`
    let mut iter = composers.iter(); // Get an iterator yielding &Person

    while let Some(composer) = iter.next() {
        // Inside the loop, 'composer' is of type &Person
        println!("{}, born {}", composer.name, composer.birth);
    }

    // The 'composers' vector is still valid here
    println!("Vector length after iter() loop: {}", composers.len());
}
```

*   **Memory Usage:** Exactly the same as the original `for composer in &composers`. The `composers` vector's memory is not modified by the loop. The iterator (`iter`) holds a reference into the vector's data.

**Snippet 2 Refactored (Equivalent to `for composer in composers`)**

This uses the `into_iter()` method, which is the explicit way to get an iterator that yields values (`T`) and consumes the vector.

```rust
struct Person {
    name: String,
    birth: i32,
}

fn main() {
    let mut composers = Vec::new();
    composers.push(Person {
        name: "Palestrina".to_string(),
        birth: 1525,
    });
    composers.push(Person {
        name: "Dowland".to_string(),

        birth: 1563,
    });
    composers.push(Person {
        name: "Lully".to_string(),
        birth: 1632,
    });

    // Refactored loop equivalent to `for composer in composers`
    // `composers.into_iter()` consumes the vector and yields Person by value
    let mut iter = composers.into_iter(); // Get an iterator yielding Person

    while let Some(composer) = iter.next() {
        // Inside the loop, 'composer' is of type Person (the value is moved here)
        // The String inside 'composer' is dropped when 'composer' goes out of scope
        println!("{}, born {}", composer.name, composer.birth);
    }

    // The 'composers' vector is NOT valid here because it was moved into into_iter()
    // println!("Vector length after into_iter() loop: {}", composers.len()); // This line would cause a compile-time error!
}
```

*   **Memory Usage:** Exactly the same as the original `for composer in composers`. The `composers` vector is moved into the `into_iter()` call. Each `Person` is moved out of the vector's buffer into the `composer` variable during iteration, and its memory is deallocated when the variable goes out of scope. The vector's underlying buffer is deallocated when the iterator (which now owns the buffer) goes out of scope after the loop finishes.

**Difference between the Refactored Versions**

The key difference between the two refactored versions is which explicit iterator method is called on the vector:

1.  **`composers.iter()`**: This is syntactic sugar for `(&composers).into_iter()`. It operates on a **shared reference** to the vector and produces an iterator (`std::slice::Iter`) that yields **shared references (`&Person`)**. The original vector remains untouched and fully usable after the loop.
2.  **`composers.into_iter()`**: This calls the `IntoIterator` trait method directly on the **vector value**. It **consumes** the vector and produces an iterator (`std::vec::IntoIter`) that yields the elements **by value (`Person`)**. The original vector is invalidated after this call.

Both methods leverage the `IntoIterator` trait, but they do so on different receivers (`&Vec<T>` vs `Vec<T>`), resulting in iterators that yield different types (`&T` vs `T`) and have different effects on the source collection (borrow vs. consume).

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*