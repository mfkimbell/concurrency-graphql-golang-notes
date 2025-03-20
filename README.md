## concurrency-notes

### Sequential processing
* Each task waits for the previous one to complete before starting.
* This is the simplest form of processing, but it can be inefficient if tasks are I/O bound or if there is a need for parallelism.

### Parallel processing
* Tasks run simultaneously, taking advantage of multiple cores or processors.
* This can be achieved through multi-threading or multiprocessing, depending on the use case.
* Ideal for CPU-bound tasks where tasks are computationally intensive.
  
<img width="452" alt="Screenshot 2025-02-19 at 10 42 25 PM" src="https://github.com/user-attachments/assets/d25621ab-d6fe-4431-9e51-5d2a87c471a8" />

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
* Can be parallel or concurrent
  
#### When Multithreading Can Run at the Same Time (Parallel):
* Multi-Core Processors: If a program is running on a multi-core processor, different threads can run on separate cores. This means true parallel execution is possible, and multiple threads can execute simultaneously.
* Languages/Environments Without GIL: In languages that do not have restrictions like Python's Global Interpreter Lock (GIL), multithreading can achieve true parallelism (e.g., Java, C++).
* Python has a GIL lock for example, so multithreading will always be concurrent

### Threads vs. Event Loop:
* Multithreading uses multiple threads to achieve parallelism, which can lead to more complex code due to the need for synchronization mechanisms like locks.
* Asynchronous programming typically uses an event loop (especially in environments like JavaScript and Python’s asyncio), where tasks are executed in a single thread, but in a non-blocking, cooperative manner.


### Multiprocessing
* Involves running multiple processes, each with its own memory space.
* This is more memory-intensive than multithreading, but it avoids the complexities of shared memory and is better suited for CPU-bound tasks.
* Each process can run on a separate CPU core, making it highly efficient for parallel processing of tasks.
* Each process has it's own memory

# GoLang

Types go after variables
```
x := 5           # type inference
var y int = 7    # manual typing
```


#### Interfaces kind of act like classes 
Here’s a **brief Go code snippet** showing **structs**, **interfaces**, and how you might **register** an implementation of an interface:

```go
package main

import "fmt"

// 1. Define an interface
type Handler interface {
	HandleRequest(data string)
}

// 2. Create a struct that implements the interface
type MyHandler struct {
	Name string
}

// 3. Implement the interface method on the struct
func (h *MyHandler) HandleRequest(data string) {
	fmt.Printf("Handler %s received: %s\n", h.Name, data)
}

// 4. A function to "register" or accept an interface
func RegisterHandler(h Handler) {
	fmt.Println("Registering handler...")
	h.HandleRequest("Some Data")
}

func main() {
	// 5. Create a struct instance that satisfies the interface
	myHandler := &MyHandler{Name: "ExampleHandler"}
	
	// 6. Register your interface implementation
	RegisterHandler(myHandler)
}
```

### **What’s Happening?**
1. **`Handler interface`**: Declares a function signature (`HandleRequest`).
2. **`MyHandler struct`**: A concrete type that implements `HandleRequest`.
3. **`func (h *MyHandler) HandleRequest(...) {}`**: A pointer receiver method that satisfies the `Handler` interface.
4. **`RegisterHandler(h Handler)`**: Accepts **any** type that implements `Handler`.
5. **`RegisterHandler(myHandler)`**: Passes a struct instance that implements `Handler`, so the code compiles and runs.

Thus, **interfaces** define **what methods** a type must have, and **structs** actually **implement** those methods. The `RegisterHandler` function **accepts any struct** that satisfies the interface, allowing for flexible “registration” of functionality.

#### Concurrency in Go Video
https://www.youtube.com/watch?v=LvgVSSpwND8

### Go Routines:
* Go routines will stop executing when Main funciton finishes exeucuting without a wait group
* Go routines are lightweight threads of execution in Go. They allow tasks to run concurrently within the same application. While they enable concurrency, they do not inherently provide parallelism. Go routines are managed by the Go runtime, which schedules them on available CPU cores.
* Go routines are much lighter than traditional threads. You can spawn thousands or even millions of Go routines without the same overhead that you would encounter with operating system threads.
* Concurrency means that multiple tasks are making progress simultaneously, but it doesn’t necessarily mean they are running at the same time on different cores (which is what parallelism does).
  
* **HOWVER** In Go, goroutines are designed to run concurrently, which means they can execute in parallel if there are enough available CPU cores

* Go routines run in the background, they don't hold up the main execution

### Channels:

* Channels in Go are used for communication between Go routines. They allow Go routines to synchronize their operations and pass data between them safely.
* In your example, strChan := make(chan string) creates a channel for strings, which is then used to transmit data between the main program and the Go routine executing the RequestTranslate function.
* grabbing from a channel is blocking
* 
<img width="1249" alt="Screenshot 2024-08-10 at 9 45 01 AM" src="https://github.com/user-attachments/assets/92a392ed-9c0f-41f5-81c4-b7b6fb7a406a">

* This will never execute since the <- c is waiting on a reciever, but it'll never get to the reciever, needs to be in a separate go routine. or you can use a buffered channel ` c := make(chan string, 2) ` this will only block when the channel is full
  
<img width="416" alt="Screenshot 2024-08-10 at 9 50 43 AM" src="https://github.com/user-attachments/assets/d4edc10b-8c41-4e14-afe8-ab00a1ed9986">

* here if we used a for loop in the main, the timing difference wouldn't change since it's sequential, but if we use the `select` keyword with a switch case, we can execute the channel that's ready.
  
<img width="662" alt="Screenshot 2024-08-10 at 9 56 32 AM" src="https://github.com/user-attachments/assets/513f6e75-2761-4a93-a8a2-514d699a5c80">

### Wait Groups:

* Wait Groups (sync.WaitGroup) are used to wait for a collection of Go routines to finish executing. It provides a way to block the main program until all Go routines have completed, similar to how await works in asynchronous programming in other languages like JavaScript or Python.
* The wait group is passed into the Go routine and signals when the routine has finished, allowing the main program to continue execution after all routines are done.

We use Add() to add to wait groups, wg.Done() to decrement wait groups. We use defer wg.done() to automaticaly call wg.done() at the end of a function.

### Worker pools
*this will execute on all 4 cores of your cpu if you ahve 4 cores. (400% cpu utilization). we queue up workers, make 100 fib(n) jobs. The workers are just waiting for things to enter the channel. 
<img width="611" alt="Screenshot 2024-08-10 at 10 03 50 AM" src="https://github.com/user-attachments/assets/36e9edb2-9534-49c5-bef9-b78df454ad69">


```golang
package main

import (
    "fmt"
    "sync"
    "time"
)

func worker(id int, wg *sync.WaitGroup) {
    defer wg.Done() // Decrease the WaitGroup counter when the function completes
    fmt.Printf("Worker %d starting\n", id)
    time.Sleep(time.Second) // Simulating some work
    fmt.Printf("Worker %d done\n", id)
}

func main() {
    var wg sync.WaitGroup

    numWorkers := 3
    wg.Add(numWorkers) // Increment the WaitGroup counter by the number of workers

    for i := 0; i < numWorkers; i++ {
        go worker(i, &wg) // Start each worker as a goroutine
    }

    wg.Wait() // Wait for all workers to complete
    fmt.Println("All workers have completed")
}
```

### Mutex
* prevents simultaneous accesss (writes in this case) to a resource. Simultaneous writes will crash the code
  
<img width="425" alt="Screenshot 2025-02-19 at 10 59 01 PM" src="https://github.com/user-attachments/assets/0452ba31-8d98-4f9e-a0f1-99065d880fb6" />

# GraphQL

<img width="567" alt="Screenshot 2024-08-09 at 4 30 17 PM" src="https://github.com/user-attachments/assets/1fa08c05-13bf-4a27-8c23-c3053eee5bc0">

It's a middleman:

Backend -> GraphQL -> Frontend

Frontend -> GraphQL -> Backend

### GraphQL Overview

GraphQL is a query language for APIs that allows clients to request specific data they need, making it highly efficient for fetching data from servers. It was developed by Facebook and provides an alternative to RESTful APIs by giving clients more control over the data they receive.

### Schema

GraphQL **schema** defines the structure of data that clients can request from the API. It includes:

* **Types:** Define object types with fields (e.g., `User`, `Post`) and their data types (`String`, `Int`, custom types).
* **Queries:** Define operations for fetching data (e.g., `getUser`, `getPosts`).
* **Mutations:** Define operations for modifying data (e.g., `createUser`, `updatePost`).

Example GraphQL schema:

```graphql
type Query {
  getUser(id: ID!): User
  getPosts: [Post]
}

type Mutation {
  createUser(input: CreateUserInput!): User
  updatePost(id: ID!, input: UpdatePostInput!): Post
}

type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post]
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
}
```

### Resolvers
Resolvers are functions that define how GraphQL fields are resolved. They fetch the actual data for each field requested in a GraphQL query or mutation.
```graphql
const resolvers = {
  Query: {
    getUser: (parent, { id }, context, info) => {
      // Logic to fetch user data by ID
    },
    getPosts: (parent, args, context, info) => {
      // Logic to fetch list of posts
    },
  },
  Mutation: {
    createUser: (parent, { input }, context, info) => {
      // Logic to create a new user
    },
    updatePost: (parent, { id, input }, context, info) => {
      // Logic to update a post by ID
    },
  },
  User: {
    posts: (parent, args, context, info) => {
      // Logic to fetch posts authored by a user
    },
  },
  Post: {
    author: (parent, args, context, info) => {
      // Logic to fetch the author of a post
    },
  },
};
```

### Queries and Mutations
Queries in GraphQL are used to fetch data, while mutations are used to modify data. They are defined in the schema and resolved by corresponding resolver functions.

Example GraphQL queries and mutations:

```graphql
# Query to fetch a user by ID
query GetUser {
  getUser(id: "123") {
    id
    name
    email
    posts {
      id
      title
    }
  }
}

# Mutation to create a new user
mutation CreateUser {
  createUser(input: { name: "Alice", email: "alice@example.com" }) {
    id
    name
    email
  }
}
```


### Overfetching and Underfetching
* Overfetching and underfetching are common issues in API design:

* Overfetching: Occurs when the API returns more data than needed, wasting bandwidth and processing resources.
* Underfetching: Occurs when the API does not provide enough data in a single request, leading to multiple round-trips and slower performance.
* GraphQL helps mitigate these issues by allowing clients to specify exactly what data they need in each request, reducing overfetching and underfetching.
