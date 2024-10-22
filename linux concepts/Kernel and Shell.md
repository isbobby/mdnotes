The term `operating system` is common used to describe two concepts
1. To denote the entire package consisting the central software managing a computer's resource, and all of the accompanying software tools such as CLI, GUI and such.
   
2. To denote just the central software managing a computer's resources

The term `kernel` is often used as a synonym for the second concept above.

The `kernal` performs the following main tasks
1. Process related tasks, such as scheduling and life cycle management
2. Memory Management
3. Provision of a file system
4. I/O and access to devices
5. Networking
6. Provisioning API for system calls 

Multiuser OS also generally provide user with an abstraction of Virtual Private Computer - so each user can log on to the system and operate largely independently.

### Kernel and User Mode
Kernel and user mode is a common distinction between execution mode, made to safeguard the system's resources.

More critical operations are typically only available in kernel mode and not in user mode. Such operations include executing halt instruction to stop the system, accessing the hardwares, and initiating device I/O operations.

By restricting access, we prevent users from accidentally / purposefully perform operations that would adversely affect the operation of the system.

### Perspectives - Kernel vs Process View
Most programming tasks only require us to think from a process-oriented perspective, a benefit we gain from the abstraction provided by mature kernels.

In order to understand the internal workings of a computer, it requires us to view programs from the kernel's perspective.

We can build our "kernel perspective" by considering the main tasks performed by the kernel, for example
1. how do we decide the next process to obtain CPU? and for how long?
2. how do we transfer information between devices and programs?
3. where and how do we allocate a process in the memory?
4. what APIs does the kernel provide to allow a process to perform its functionalities?

## Shell
A shell is a special purpose program designed to read and interpret commands, and execute appropriate programs in response to these commands.

There is a number of important shells that have appeared over time
1. Bourne Shell (`sh`) - all later UNIX implementations include `sh`
2. C shell (`csh`) - provides additional features not in `sh`, such as history, command line editing.
3. Korn shell (`ksh`) 
4. Bourne Again Shell (`bash`) - the most widely used shell, `sh` is also typically provided by `bash`, emulating `sh` as closely as possible

The shells are designed for shell script interpretations as well, for this, each shell also has the facilities typically associated with programming languages, such as control statements and loops.

### `shebang`
The shebang is the first line of a Bash script, indicating the interpreter that should be used to execute the script:
```
#!/bin/bash
```

### Variables
Variables assignment: `=`, note that there must be no whitespace, so `v = 1` throws error
Variables reference: `$`
```
#!/bin/bash

v=1
echo $v

output: 1
```

### Strings
Double quoted strings allows variable and command substitution, similar to `printf`, whereas single quoted string is treated as a literal
```
#!/bin/bash

sub="world"

s1="hello $sub"
s2='hello $sub'

echo $s1 # hello world
echo $s2 # hello sub
```

### Command Substitution
We can use `$()` to run commands in a script and use their output
```
#!/bin/bash

today=$(date)
echo "Today's date is $today" # Today's date is Thu Oct 17 11:12:46 +08 2024
```

### Arithmetic Substitution
Use `$(())` to do arithmetic operations
```
#!/bin/bash

x=5
y=3
sum=$((x+y))
echo $sum #8
```
### Control Structures
If statements require `if/elif`, `then` and `fi` to close the structure. Note the `;` behind `[condition]`.
```
#!/bin/bash

if [ condition ]; then

elif [ condition ]; then

else 

fi
```

Loops can iterate over a range, or iterate while condition remains true, the keyword for two loops are different and not interchangeable.
```
#!/bin/bash

for i in 1 2 3 do 
	echo $i
done

count=1
while [ $count -le 3 ]; do
	echo %count
	count=$((count+1)) # arithmetic 
done
```

### Functions
Function requires positional argument, starting from `$1`
```
#!/bin/bash

f1() {
	echo "hello world, $1"
}

f2() {
	echo "$1 $2"
}

f1 "first" # hello world, first
f2 "hello" "there" # hello there
```

