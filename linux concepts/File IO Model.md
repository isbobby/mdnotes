# I/O Universality
The most important concept in linux I/O - I/O universality. It means the same system calls `open, close, read, write` are used to perform I/O on **all types of files**, including **devices**. 

This is achieved by ensuring each file system and device driver implements the same set of I/O system calls.

The kernel translates the I/O request into appropriate file system / device operation. This allows program employing these system calls to work on any types of files.

The kernel essentially provides one file type - a sequential stream of bytes.

# [File Descriptor](Linux%20File%20Descriptors)
The I/O system calls refer to files using a file descriptor, a usually small non-negative integer. This file descriptor is obtained with the `open` call.

A program also inherits three special file descriptor when it starts, they are
- 0: standard input
- 1: standard output
- 2: standard error
In an interactive program, these descriptors are normally connected to the terminal. Linux also provides file control `fcntl` function, which manipulates file descriptors.

# Command Deeper Looks
## `open()`
`open()` takes `(pathname string, flags int)`. When given a symbolic link, the `open` function will automatically dereference first.

This system call either 
1. opens an existing file
2. creates, then open the created file

The function will then return a file descriptor to the calling process. SUSv3 (unix spec) specifies `open` will always return the lowest file descriptor value possible for the process.

The flags determined the allowed behaviour of the `fd`, for example
- `O_APPEND` only append - all writes are appended at the end 
- `O_ASYNC` generate a signal when I/O becomes possible, this feature is termed *[signal-driven I/O](Signal%20Driven%20IO)*. To enable this, we first need to set this flag using `fcntl() F)SETFL` operation.
- `O_CREAT` creates a file if not exists, if we do this, we must also supply a `mode` argument in the `open` call to set the file permission.

For any errors, the `open` function returns `-1`. We can match the `errno` with the cause of the error. Some errors include `EACCES`, `EROFS`.

## `create()`
Looking above we see `open()` can be used to create new files. This was not the case in earlier implementation, instead, the `create()` system call was used to create and open a new file.

## `read()`
Reads data from the open file referred to by the provided `fd`, we also supply `buffer` - the memory address where the data retrieved can be stored, and `count` - the maximum number of bytes to read. Note that the memory buffer of correct size has to be allocated first.

A successful read returns the number of bytes read. On error, `-1` is returned instead. Due to the universality of I/O, we can call `read()` on things such as terminal and socket. We need to consider these other cases later.

Consider the following program
```
#define MAX_READ 20
char buffer[MAX_READ];

// assume no error
read(STDIN_FILENO, buffer, MAX_READ);

printf("Data: %s\n", buffer);

//output
./a.out 
hi!
Data: hi!
�ʥ�
```

What's with the trailing gibberish? Upon inspection, we realised `read` does not automatically append a terminating null byte at the end of `buffer` - this is expected because `read` does not want to include any data that's not in the `fd` destination. 

We can solve this by making use of the return bytes from `read`, and set this to `\0` in the buffer
```
#define MAX_READ 20
char buffer[MAX_READ];
ssize_t numRead;

numRead = read(STDIN_FILENO, buffer, MAX_READ);
buffer[numRead] = '\0';

printf("Data: %s\n", buffer);
```

The above will terminate nicely.

## `write()`


## `close()`
Closes an open file descriptor, this frees it for subsequent reuse by the same process. When a process terminates, all of the file descriptors automatically close.

It's a good practice to always close the `fd` especially in long running process, this is because the process may run out of usable `fd`s (since `open` always take the minimum `fd`+1).

`close` returns `-1` on error, also helps to catch unopened `fd`s or close the same `fd` twice.

## `lseek()`
Changes the file offset. 