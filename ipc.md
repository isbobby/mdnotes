# Inter Process Communication
Allows processes to talk to each other. Mainly by shared memory, or message passing.

## Shared Memory
Shared memory - sender and receiver communicate by reading and writing to the same region of memory.

The sender process will have to declare a region of memory to be shared. Then, the sender process will attach this region to its address space.

Then, another process will gain access to the shared memory. This can be done when it's first forked from the sender process. (Q: How is process resource handled when it's first forked)

To clean up, both processes can detach from the shared memory region.

## Message Passing
Message passing allows two processes to communicate by sending and receiving a message.

It can be configured to have direct / indirect addressing, synchrnous / asynchronous, buffered / unbuffered.

A communication link has to be created between two processes, and messages are delivered into a `mailbox` of the receiver process.

On Linux, the implementation can be achieved with message queue, pipes and sockets.

### Message Queue


### Pipes

### Sockets
Sockets can be used for communication between processes, either on the same machine or over a network.
