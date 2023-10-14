# C++ Concurrency in Action
by ANTHONY WILLIAMS

#### Suggested prior learning
- *RAII*
- rvalues and lvalues
- std:move

Books:
- The C++ Programming Language (First Chapter)

## Chapter 1: Hello, world of concurrency in C++!

- **C++11** Standard introduces support for multithreaded programs within the STL
- *Task Switching* provides the ILLUSION of concurrency
- *Hardware Concurrency* achieved with multiple processors or multiple cores
- *Context Switching* between tasks carries overhead
- Task Switching is used alongside Hardware Concurrency

#### Multiple Processes
- More overhead (starting a process, managing it, providing protection)
> NOTE: On Linux, a thread is a process that shares its memory
- Easier to write safe concurrent code (e.g. Erlang)
- Can use different machines over the network
- Communication is done through **signals, sockets, files, pipes, etc.**
- No support from the C++ standard for interprocess communication

#### Multiple Threads
- On some OS's: Threads are lightweight processes
- Shares address space, global vars, can pass pointers and references
- Careful consideration of communication between threads and sharing data
- View of data as seen from each thread must be consistent when accessed
- Favoured approach to concurrency in most languages

### When to use Concurrency?
**Two main reasons: SEPARATION OF CONCERNS and PERFORMANCE**

#### Separation of Concerns
- Group related code and separate unrelated code
- Avoid making calls to different, unrelated areas of code in a procedure
- Based on conceptual design, not increase of throughput
- Number of threads is independent of CPU cores (Task Switching)
- Logic in each thread is made simpler

#### Performance
1. Divide a single task into parts and run in parallel
   - *Task Parallelism*:
     Division in terms of processing, a task is divided in several steps
   - *Data Parallelism*:
     Each thread performs the same operation on different parts of data
   
   Algorithms which are prime candidates for parallelism: *Embarrassingly Parallel*
   Also known as: *Naturally Parallel*, *Conveniently Concurrent*
   They have good scalability
   Other algorithms may only be divided into a fixed number of parallel tasks
   
2. Process more data in the same amount of time
   - Make new features possible or enhance existing ones (e.g. higher video resolution)
   - Application of data parallelism
   - One chunk of data still takes the same time

![](../diagrams/D0_0.png)

> Additional Reading: The Free Lunch is Over, Sutter H.

### When not to use Concurrency?
**When the potential benefit is outweighed by cost or complexity**

- Code is harder to understand
- Intellectual cost
- Higher amount of bugs
- Potential gain should justify development time and costs
- Performance gain may not be huge due to overhead
- Threads are limited and can exhaust memory (specially in 32bit archs) [^1]
- Important to **adjust the number of threads**
- Concurrency is mostly worth using for performance critical parts, or clarity of design


#### History of C++ Multithreading
- C++98 lacks a definite memory model, so compiler extensions are needed to write multithreaded code.
- Boost library adds support for multithreading
- Problems due to no memory model with portability between platforms

#### C++11
- Thread aware memory model
- Classes to create and manage threads, protect shared data, synchronise threads and low level atomics
- Based on boost thread library

- What about efficiency?
  *Abstraction Penalty*: Penalty in performance for using high-level facilities vs low-level platform-specific directly
  Very low abstraction penalty on most platforms
  Atomic types replace assembly, platform-specific code
  More portable and easier to maintain
  Design of application is major factor in performance

- Platform specifics
  Types in the C++ thread library may offer `native_handle()` to use native implementation API directly


#### Hello World of Concurrency

```
#include <iostream>
#include <thread>

void hello()
{
  std::cout << "Hello World" << std::endl;
}

int main()
{
  std::thread t(hello); // starts thread
  t.join(); // wait for thread to finish
}
```


### Summary
- Concurrency, multithreading concepts
- When to/not to use concurrency?
- History of C++ support

[^1]: Thread pools may alleviate this

## Chapter 2: Managing Threads

- At least one thread in all programs (main)
- Starting a thread is constructing an `std::thread` object

```
#include <thread>
void do_some_work();

std::thread my_thread(do_some_work);
```

- `std::thread` works with any callable type, including function objects (functor)
  - In this case, the object is copied into the new thread
  - Don't pass functor directly, use double parentheses, brackets, or named variable
  ```
  class bg_task {...}
  std::thread t(bg_task()); // don't
  std::thread t((bg_task())); // okay
  std::thread t{bg_task()}; // okay
  ```
- Constructor also accepts a lambda expression
- **Can either wait for thread to finish (with `.join()`) or separate from it (with `.detach()`)**
- **Ensure data accessed by thread is valid as long as thread needs to access it**
- It is a bad idea to create a thread which accesses local variables and not wait for it to finish

### `.join()`

- Clears up storage and dissasociates thread with object, `.joinable()` will return false.
- Call to join may be skipped if an exception is thrown after the thread is created
- It is a good idea to use RAII to ensure the thread is joined before it goes out of scope

### `.detach()`

- The thread is no longer joinable
- Ownership will be passed to the C++ Runtime Library
- Also called *daemon* threads (as in UNIX)

#### Passing arguments to threads
- `std::thread t(f, args...);`
- Arguments are copied into the thread
- Be careful when passing a `char[]` that implicitly converts to `std::string` (or similar conversions)
  as conversion actually occurs in the new thread
- Passing references must be done by wrapping them in `std::ref(data)`

#### Member functions
```
X my_x;
std::thread t(&X::f, &my_x, args..);
```
> I believe `std::bind` may also be used

### Move semantics
- Remember to use `std::move` when necessary (e.g. `std::unique_ptr`) when passing arguments
- `std::thread` itself movable but cannot be copied
  `std::thread t2 = std::move(t1);`
  Will fail if object assigned to already has an associated thread.
- Ownership can be moved in and out of a function
- Can create a thread wrapper class that takes ownership of thread
- Allows to keep threads inside containers which are move aware such as `std::vector`


`std::thread::hardware_concurrency()`
- Provides a hint of how many threads can run truly concurrently

*Oversubscription*: more threads than the hardware naturally supports

### Unique Identifiers
- Each thread has a unique ID of type `std::thread::id`
- Retrieve with `get_id()` member function
  If the object has no associated thread, it returns a default constructed id
  For the current thread, call `std::this_thread::get_id()`
- Can be copied and compared (most comparison operators), also sorted
- Can be used as keys with `std::hash<std::thread::id>`
- Sometimes used to check if thread needs to behave differently
- Or used to store data for each thread in an associative container
- Can be output to console (implementation-dependant)

### Summary
- Threads: join, detach, passing arguments, move semantics
- Division of work, thread id's
