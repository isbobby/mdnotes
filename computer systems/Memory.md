# Background
Computer programs can use any amount of data when it's running. 

We can't store all these data in the processor, as processors have very limited storage space. We also don't want to store the data in the slow (in-comparison) hard disks.

Our solution to solve this space issue is to develop and use memory - a reasonably big, reasonably fast volatile storage medium.

`volatile - the data storage requires continuous power supply to maintain the data`

## More Considerations
In our modern computers, we usually have many processes running at the same time. Use `ps -e | wc -l` to see how many active processes are there.

To ensure all these processes can run smoothly, we want the single memory component to serve multiple active processes.

In addition, we want to provide memroy over-subscription, where the processes can use more memory than the amount of physical memory.

# Mechanisms
It will be helpful to revisit a few critical components in a processor.
1. program counter
2. processor
3. memory subsystem in processor

The processor is fast but can only hold a small amount of data, it usually needs to fetch data from memory.

The processor fetches instructions by accessing the instruction located at the address speicifed by the program counter.

The processor fetches data by providing the memory subsystem a physical memory address, the memory subsystem retrieves the data from RAM.

We need to supply memory reliably to processors.

