An array is a data structure consisting of a collection of elements of the same memory size, occupying **contiguous** memory locations. Each element can be identified by its index. The memory location of each element can be computed directly from its index with a formula, which supports $O(1)$ look up.

Arrays effectively exploit the addressing logic of computers, where memories are one-dimensional arrays of words. This structure aligns well with how processor operations are designed, as they are often optimised for operations on contiguous memory regions.

One example is [cache locality](Cache%20Locality), where the processor leverages spatial locality by loading adjacent elements from the memory region into the cache. The processor may also perform sequential prefetching, predicting and loading subsequent memory blocks into the cache when a **sequential read pattern** is detected. Both optimisations improve performance by reducing latency associated with frequent memory access.

# Dynamic Arrays
Built on top of static contiguous array, a dynamic array is an abstract data structure which supports dynamic resizing and appending on top of standard array operations. Dynamic resizing allows its memory usage to be more flexible and efficient.

The key features are
- Resizable, often abstracted and done automatically by the programming language
- Contiguous memory, which maintains $O(1)$ look up and other benefits 
- Size is determined in runtime 

The automatic resizing is done by checking capacity whenever `append()` is called, where the growth factor is `2`. This means resizing happens logarithmically with respect to the number of elements added.

A new memory block is allocated to hold the existing and new array. We need a new memory region to guarantee the array remains contiguous. The existing elements are visited then copied over. Because of copying, each resizing operation is $O(N)$.
# Algorithms
## [[Sort]]


## Creation, Referencing, Copy


