The I/O system calls refer to files using a file descriptor, a usually small non-negative integer. This file descriptor is obtained with the `open` call.

A program also inherits three special file descriptor when it starts, they are
- 0: standard input
- 1: standard output
- 2: standard error
In an interactive program, these descriptors are normally connected to the terminal. Linux also provides file control `fcntl` function, which manipulates file descriptors.
## In a [Process](Linux%20Processes.md)
To track the relevant file descriptors, each process also maintains a **File Descriptor Table**. This table resides in the process' private memory, and stores the pointer to the actual open file in the kernel's file table.

When a process is forked, this particular table is copied, such that the child process have access to parent's file descriptor. This allows a child process to write to the same file.

We can start a simple HTTP server listening on localhost 3000, get the `pid` using `ps`, and use `lsof -p <nginx_pid>` to see the file descriptors in use. The `lsof -p` output contains multiple columns:
1. `Command` and `PID` - the command which ran the process, and the process ID of the running program
2. `USER` - the username or user ID that owns the process
3. `FD` - this represents the file descriptor used by the process to interact with the file or resource, it has two parts - numeric prefix and string suffix. The string suffix determines the type of file descriptor.
4. `TYPE` - this represents the type of the file - `REG` are regular files, `CHR` are character device, `SOCK` are sockets, `FIFO` are pipes.
5. `DEVICE` - this represents the file size or offset in bytes, or it shows the offset / memory address of the sockets or devices.
6. `NODE` - the `inode` number of the file on the file system. This `inode` is a unique identifier for the file in the file system.
7. `NAME` - name or path of that resource.

We expected a file descriptor matching the unix socket, but the output contains many other files - such as `.so` - shared dynamic library files.

## A Limited Resource
A key perspective is to consider file descriptors as **limited resource**,
1. The OS will place a **system wide** limit on the number of file descriptors, this can be checked using `ulimit -n`
2. Then, the OS also has a per-process limit in the file descriptor table size, exceeding this limit results in the process unable to open new files
3. Since OS imposes a system limit, each process should do best effort in keeping the file descriptor count low to ensure fair sharing
5. The OS uses memory to maintain file descriptors - the more file descriptors we maintain, the more memory we use
This means it's always a good practice to close any unused file descriptors.

## Ephemeral vs Non Ephemeral
Ephemeral is another distinction between file descriptors.

Ephemeral file descriptors are short-lived file descriptors used for temporary operations like socket connections, temporary files, or inter-process communication. They are opened, used, and closed quickly to free resources.

Take [networking](Linux%20Networking.md) server and client as example. A web server is usually a long running process, it will listen at the specified port. This port is not ephemeral.

When a client sends a request, a connection is established - this is usually done with the function `accept()`.  Instead of using the original server socket, a **new client socket is created, and the representing FD** is returned:
```
int client_socket_fd = accept(sockfd, &client_addr, &client_len);
```

This socket is closed after the server has returned a response to the client. If we inspect the process with `lsof -p pid` again, we can see
1. while the server is processing the request, the connection is maintained and there is a corresponding `fd` in the file descriptor table
2. after the server has finished processing the request, this socket is closed, and the corresponding `fd` is also closed

> To verify the above, it's easier to add a few seconds of delay in the HTTP handler to have more time to execute and examine the output from `lsof -p`


