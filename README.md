## concurrency-notes

### Sequential processing
* Each task waits for the previous one to complete before starting.
* This is the simplest form of processing, but it can be inefficient if tasks are I/O bound or if there is a need for parallelism.

### Parallel processing
* Tasks run simultaneously, taking advantage of multiple cores or processors.
* This can be achieved through multi-threading or multiprocessing, depending on the use case.
* Ideal for CPU-bound tasks where tasks are computationally intensive.

### Concurrent vs Parrallel execution:
* Concurrent means they don't wait on each other (async)
* Parrellel means they execute simultaneously

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

# GoLang


### Go Routines:
* Go routines are lightweight threads of execution in Go. They allow tasks to run concurrently within the same application. While they enable concurrency, they do not inherently provide parallelism. Go routines are managed by the Go runtime, which schedules them on available CPU cores.
* Go routines are much lighter than traditional threads. You can spawn thousands or even millions of Go routines without the same overhead that you would encounter with operating system threads.
* Concurrency means that multiple tasks are making progress simultaneously, but it doesn’t necessarily mean they are running at the same time on different cores (which is what parallelism does).

### Channels:

* Channels in Go are used for communication between Go routines. They allow Go routines to synchronize their operations and pass data between them safely.
* In your example, strChan := make(chan string) creates a channel for strings, which is then used to transmit data between the main program and the Go routine executing the RequestTranslate function.

### Wait Groups:

* Wait Groups (sync.WaitGroup) are used to wait for a collection of Go routines to finish executing. It provides a way to block the main program until all Go routines have completed, similar to how await works in asynchronous programming in other languages like JavaScript or Python.
* The wait group is passed into the Go routine and signals when the routine has finished, allowing the main program to continue execution after all routines are done.
