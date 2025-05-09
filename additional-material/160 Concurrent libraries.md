# Concurrent libraries

## What is what are we looking for?

1.  **Synchronous Multithreading:** Using OS threads managed by the operating system, communicating via standard synchronization primitives (like mutexes, channels) or specialized concurrent data structures.
2.  **Asynchronous Programming:** Using a runtime (like Tokio or async-std) to manage lightweight tasks (futures) on a pool of threads, primarily for I/O-bound workloads.
3.  **Data Parallelism:** Distributing computations across multiple cores, often on data structures like vectors or slices.

The popular libraries often specialize in one or more of these areas.

Here are some of the most prominent ones:

1.  **`crossbeam`**:
    *   **Description:** A collection of highly efficient, generally lock-free or minimally-locking concurrent data structures and synchronization primitives. It provides very fast concurrent queues, stacks, and a particularly well-regarded MPMC (Multi-Producer, Multi-Consumer) channel implementation. It often serves as a lower-level building block for other concurrency abstractions or for performance-critical specific needs.

2.  **`tokio`**:
    *   **Description:** The dominant asynchronous runtime for Rust. It provides an event loop, a scheduler to run asynchronous tasks (futures), and a comprehensive ecosystem of libraries for network programming (TCP, UDP, HTTP), file I/O, timers, and inter-task communication (async channels, mutexes). It's designed for building high-performance, high-concurrency applications, particularly I/O-bound services.

3.  **`async-std`**:
    *   **Description:** Another popular asynchronous runtime for Rust, often seen as an alternative to Tokio with a focus on adhering closely to standard library traits and feeling more like a natural extension of `std`. It also provides an event loop, task scheduler, and asynchronous I/O components, aiming for simplicity and alignment with Rust's core principles.

4.  **`rayon`**:
    *   **Description:** A data-parallelism library that makes it easy to parallelize loops and iterate over collections in parallel. By simply changing `.iter()` to `.par_iter()`, you can often get significant speedups on CPU-bound computations without manual thread management. It handles splitting data and scheduling tasks across a thread pool automatically.

**Comparison Table**

| Feature                | `crossbeam`                                                                      | `tokio`                                                                                                    | `async-std`                                                                    | `rayon`                                                                                    |
| :--------------------- | :------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------- |
| **Primary Focus**      | Concurrent Data Structures, Sync Primitives                                      | Asynchronous Runtime, I/O-bound concurrency                                                                | Asynchronous Runtime, I/O-bound concurrency                                    | Data Parallelism, CPU-bound computation                                                    |
| **Concurrency Model**  | OS Threads + specialized sync/channels                                           | Async Tasks on a Thread Pool (reactor pattern)                                                             | Async Tasks on a Thread Pool                                                   | Thread Pool + work stealing (data splitting)                                               |
| **Performance**        | Extremely High (often lock-free/optimized)                                       | High (specifically for I/O concurrency)                                                                    | High (similar goal to Tokio for I/O)                                           | High (efficient parallel execution)                                                        |
| **Ease of Use**        | Medium (requires understanding sync issues)                                      | High initial cognitive load (async concepts, runtime setup), but standard patterns once grasped            | Medium initial cognitive load (async concepts, simpler API for some)           | Very High (often just changes `.iter()` to `.par_iter()`)                                  |
| **Maturity/Stability** | Very Mature, API stable                                                          | Very Mature, Widely used, API generally stable but evolves                                                 | Mature, Stable                                                                 | Very Mature, API stable                                                                    |
| **Community Support**  | Strong, foundational for other libraries                                         | Very Strong, Large ecosystem, industry standard                                                            | Good, Active development, strong standards focus                               | Strong, widely used for parallel loops                                                     |
| **Typical Use Cases**  | Building custom concurrent structures, MPMC channels, high-perf messaging queues | Network servers (web, gRPC, etc.), database clients, high-concurrency microservices, complex I/O pipelines | Similar to Tokio, preference often stylistic or based on specific features     | Parallelizing loops (`for` becomes parallel), processing large datasets on multi-core CPUs |
| **Dependencies**       | Minimal dependencies                                                             | Significant dependencies (networking, etc.)                                                                | Significant dependencies (networking, etc.)                                    | Relatively low                                                                             |
| **Blocking**           | Primitives can be blocking (e.g., `recv()`)                                      | Runtime manages *non-blocking* I/O. Blocking *computations* must be offloaded (`spawn_blocking`).          | Runtime manages *non-blocking* I/O. Blocking *computations* must be offloaded. | Designed for *blocking* CPU work (splits it across threads).                               |

**Summary:**

*   Choose **`crossbeam`** when you need highly optimized, low-level concurrent building blocks or robust, high-performance channels (like a true MPMC).
*   Choose **`tokio`** or **`async-std`** when your primary need is handling a large number of concurrent I/O operations (networking, files). Tokio is currently more dominant in the ecosystem, while async-std offers a different API flavor.
*   Choose **`rayon`** when you have CPU-bound computations (like processing data in loops) that you want to easily parallelize across multiple cores.

These libraries are not mutually exclusive; they can sometimes be used together. For example, you might use `rayon` to parallelize data processing *within* a task managed by Tokio or async-std, or use `crossbeam` channels for specific high-performance communication needs even within an async application.

---

*This custom Rust training was created by **IQSOFT - EduTech/gabor for Ericsson – © 2025**. 
All materials are exclusively for use by participants of the training. Using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.*