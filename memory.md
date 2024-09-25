# Memory
System main memory stores application and kernel instructions.

## Problem and Requirements
We want the memory to serve multiple programs. 

We want to maximise memory usage by allowing over subscription - allows programs to use more memory than the amount of physical memory.

## Basic Mechanisms
The processor is fast but can only hold small amount of data, it usually needs to fetch data from memory.

The processor fetches instructions by accessing the instruction located at the address speicifed by the program counter.

The processor fetches data by providing the memory subsystem a physical memeory address, the memeory subsystem retrieves the data from RAM.

We need to supply memeory reliably to processors.

## Virtual Memory
To satisfy the above requirement, we use virtual memory to abstract memeory management for processes.

A process primarily uses virtual memeory addresses. It does not have to worry about the underlying mechanism to map virtual address to physical address.

It also does not need to worry about accessing memeory in a safe way as most OS will raise related error.

When the memory is oversubscribed, virtual memory also abstracts swapping so processes need to worry about this process.

Because we allow oversubscription, programs larger than the main memory can also be executed.

## File System Paging
Sometimes processes write and read to memory-mapped files. These files are used as backing storage for the memory.

It has many applications, such as 
1. DBMS - data is stored in files on disk
2. Web browsers - cached web pages, to reduce network load
3. IDEs 

## Anonymous Paging / Swapping
Processes have private data - its heap and stack. Sometimes due to memory pressure they need to be swapped out. This process of swapping out processes' private data does not mapped to an actual file on the disk - hence anonymous paging.

This also means anonymous paging is transient and can be lost when the process terminates.

## On Demand Paging
Programs requires memeory by allocating with `alloc`, then writing / reading to the `alloc` region.

Even when `alloc` is called, it does not guarantee the program will use the memory region.

When the program eventually does, it will require access to the physical memory region.

This is facilitated by MMU - memory management unit, which translates the process virtual address to a physical address.

This done by checking the page table, which contains `virutal_address -> {physical_address, is_valid}` mapping.

If a valid physical address exists, then the physical address will be returned to the processor.

If not, a page fault occurs.

## Page Faults
There can be multiple scenarios where page fault happens.
1. Page does not exist, need to create new page
2. Page is present in memory but not mapped properly, can be resolved by updating page table and return the updated mapping
3. Page is not present in memory, and has to be loaded from the disk or another slower storage medium.

## Page States - a process' perspective
1. Unallocated
2. Allocated in process, but not mapped
3. Allocated in process, and mapped to the main memory
4. Allocated in process, but mapped to a swap device
