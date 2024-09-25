Topic: Stack and Heap in Go
Medium Article
Theory
Stack vs Heap
Trade-offs
Comparison with other languages
Stack
Stack - common in many languages. Stack is a collection of frames.
New function calls, such as main() adds one frame to the stack.
When main() returns, the top of the stack is popped / removed.
When a stack is popped, everything inside is "lost". For example, add() -> multiply() multiply will likely use the memory space add was using.
Hence, if we want to use something from add later in multiply, we cannot put it in add's stack.
The stack cannot grow indefinitely, as the size is limited by the OS. When the stack grows too big, the program will crash with stack overflow error.
In addition, once the stack size is determined and allocated, it is very complicated and costly to resize it later on. Hence, for objects that may later change in size, we need another storage mechanism to store them.
Stack Operation
Heap
In our programs, we can have dynamic data structures - whose size can grow and shrink.
From the discussion above, we know we can't use stack for these data structures as we don't want to resize our stack frames.
Hence, the heap memory region is created for these cases.
Note - even though the heap may remind you of binary-heap, it does not imply the implementation uses binary-heap. The heap name is used early on and got stuck.
In stack memory, we clean up the stack frame after a function has returned. However, we do not clear the heap memory when a function returns. This is because the heap memory object may still be used by other functions / stack frames.
During compile time, the compiler will check and decide where to put the program's data. If some data can't be stored safely on the stack, it will be allocated onto the heap. This is also referred to as escape to the heap.
Heap Operation
Why not JUST heap?
Seems like heap is a superior choice of managing memory since it allows storage dynamic data, so why not use heap exclusively?
The short answer is - even though heap provides mechanism to store dynamic data structure, it is slower compared to stack due to the additional action the program has to perform to allocate and get data from heap.
We will revisit this comparison in the programmatic analysis below.
Conclusion
Stack
Abstraction
When we write our go programs, we don't need to worry about stack/heap's implementation and operations as they are abstracted by go's runtime.
Programmatic Analysis
Extended Topics
Garbage Collection
Assignment