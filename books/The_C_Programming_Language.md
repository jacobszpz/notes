# The C Programming Language
by BRIAN W. KERNIGHAN & DENNIS M. RITCHIE

## Preface & Introduction

> Historical information and overall characteristics of C so may skip if already familiar.
> These characteristics are explored later anyway.

- 1978: First publication of *The C Programming Language*
- 1983: *American National Standards Institute (ANSI)* establishes a committee
  to produce *"an unambiguous and machine-independent definition of the C language"*
  which became the *ANSI standard for C*.

### C
- General purpose language, not specialised and not particularly 'big',
  with not many restrictions
- Originally designed for and implemented on UNIX by *Dennis Ritchie*
- Not tied to any particular hardware or system
- *wears well as one's experience grows*
- Ideas for C stem from BCPL (by *Martin Richards*), which influenced
  B (by *Ken Thompson*), written for the first UNIX system in 1970
- Unlike B and BCPL which are typeless, C provides data types
  - Characters, integers, floating-point numbers
  - Derived data types such as pointers, arrays, structures, unions
- Expressions are formed from operators and operands
- Any expression can be a statement
- Machine-independent address arithmetic with pointers
- Fundamental control-flow constructions
- Function may return values including basic types, unions, structures,
  pointers, and can be called recursively
- Variables may be declared in a block-scope
- Different variable scopes and visibilities
- Preprocessor which runs at compile time
- Relatively low-level
- No heap or garbage collection
- No built-in file access methods, which instead must be provided
  as high-level functions that are to be explicitly called
- Most implementations have a reasonable collection of such functions
- Only straightforward, single-thread control flow
  - No multiprogramming, parallelism, synchronisation or coroutines
- Small size means can be picked up quickly

#### ANSI Standard
- New syntax for function declaration and definition
- Other minor changes
- Definition of a standard library
  - Accessing OS
  - Input/Output
  - Memory allocation
  - String manipulation
  - Modelled after UNIX I/O library
- Improved portability

*Programmers know what they are doing, but must state their intentions explicitly*

> Chapter 1 introduces basic C syntax, later chapters (2-6) build on this
