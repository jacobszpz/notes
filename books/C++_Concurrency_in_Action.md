# C++ Concurrency in Action
by ANTHONY WILLIAMS

## Chapter 1: Hello, world of concurrency in C++!

- **C++11** Standard introduces support for multithreaded programs within the STL
- *Task Switching* provides the ILLUSION of concurrency
- *Hardware Concurrency* achieved with multiple processors or multiple cores
- *Context Switching* between tasks carries overhead
- Task Switching is used alongside Hardware Concurrency

### Multiple Processes
- More overhead (starting a process, managing it, providing protection)
> NOTE: On Linux, a thread is a process that shares its memory
- Easier to write safe concurrent code (e.g. Erlang)
- Can use different machines over the network
- Communication is done through **signals, sockets, files, pipes, etc.**
- No support from the C++ standard for interprocess communication

### Multiple Threads
- On some OS's: Threads are lightweight processes
- Shares address space, global vars, can pass pointers and references
- Careful consideration of communication between threads and sharing data
- View of data as seen from each thread must be consistent when accessed
- Favoured approach to concurrency in most languages

