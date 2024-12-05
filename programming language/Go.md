# Topics
- Go GC
- [[Go Slice]]
- [[Concurrency in Go]]
- 

# GC
- Go maintains stack and heap memory, stack are used for function calls and variables scoped within a function, heaps are used for long lived references and objects
- These references to objects form an object graph, unlike function calls, these objects continue to use memory allocated after their calling function terminates
- Hence, Go employs the garbage collector to recycle heap memory
- This is done in two phases, first, completely mark out all unused objects, next, sweep the marked objects - mark and sweep
- The tuning parameter `GOGC` allows a trade off between **CPU time** and **Memory** - we can run GC very frequently at `GOGC=1`, to keep heap memory to the minimum, but it leads to the program spending a lot of time on GC. We can set GC to off, which allows the heap to grow indefinitely, but do not incur any CPU time for garbage collection

# Channel
[`hchan`](https://github.com/golang/go/blob/4fc9565ffce91c4299903f7c17a275f0786734a1/src/runtime/chan.go#L17-L29) is the central data structure for a channel, 

with send and receive linked lists (holding a pointer to their goroutine and the data element) and a `closed` flag. 

There's a `Lock` embedded structure that is defined in runtime2.go and that serves as a mutex (futex) or semaphore depending on the OS. 

Q: mutex vs semaphore

https://shubhagr.medium.com/internals-of-go-channels-cf5eb15858fc

https://docs.google.com/document/u/0/d/1yIAYmbvL3JxOKOjuCyon7JhW4cSv1wy5hC0ApeGMV9s/pub?pli=1 -> two architypes of channel (sync vs async), and the relation to interfaces such as `select`, `close`

channels use lock for synchronisation internally 

excalidraw on the dependency - same lock used in other package?

also touches GO scheduling, channel <> scheduler 



