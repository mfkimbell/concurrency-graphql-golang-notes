## concurrency-notes

### Sequential processing
* Each task waits for the previous one to complete before starting.
* This is the simplest form of processing, but it can be inefficient if tasks are I/O bound or if there is a need for parallelism.

### Parallel processing
* Tasks run simultaneously, taking advantage of multiple cores or processors.
* This can be achieved through multi-threading or multiprocessing, depending on the use case.
* Ideal for CPU-bound tasks where tasks are computationally intensive.

### Asynchronous execution
* Used for IO-bound tasks, allowing other code to execute on the same thread while IO/Calls wait to be resolved.
* In React, asynchronous execution is commonly implemented with `async` and `await` keywords, allowing non-blocking operations.
* Ideal for IO-bound tasks

### Multithreading
* Involves running multiple threads in a single process.
* Threads share the same memory space, making communication between them faster but also requiring careful handling of shared resources to avoid issues like race conditions.
* Ideal for IO-bound tasks

### Threads vs. Event Loop:
* Multithreading uses multiple threads to achieve parallelism, which can lead to more complex code due to the need for synchronization mechanisms like locks.
* Asynchronous programming typically uses an event loop (especially in environments like JavaScript and Python’s asyncio), where tasks are executed in a single thread, but in a non-blocking, cooperative manner.


### Multiprocessing
* Involves running multiple processes, each with its own memory space.
* This is more memory-intensive than multithreading, but it avoids the complexities of shared memory and is better suited for CPU-bound tasks.
* Each process can run on a separate CPU core, making it highly efficient for parallel processing of tasks.
* Each process has it's own memory
