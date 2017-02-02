# Overview

A process is program that is running (kinda). A process is also just one instance of that computer program running. Processes have a lot of things at their disposal. At the start of each program you get one process, but each program can make more processes. In fact, your operating system starts up with only one process and all other processes are forked off of that -- all of that is done under the hood when booting up.

## In the beginning

When your operating system starts on a linux machine, there is a process called `init.d` that gets created. That process is a special one handling signals, interrupts, and a persistence module for certain kernel elements. Whenever you want to make a new process, you call `fork` (to be discussed in a later section) and use another function to load another program.

## Process Isolation

Processes are very powerful but they are isolated! That means that by default, no process can communicate with another process. This is very important because if you have a large system (let's say EWS) then you want some processes to have higher privilages (monitoring, admin) than your average user, and one certainly doesn't want the average user to be able to bring down the entire system either on purpose or accidentally by modifying a process.

If I run the following code,

```
int secrets; //maybe defined in the kernel or else where
secrets++;
printf("%d\n", secrets);
```

On two different terminals, as you would guess they would both print out 1 not 2. Even if we changed the code to do something really hacky (apart from reading the memory directly) there would be no way to change another process' state (okay maybe [this](https://en.wikipedia.org/wiki/Dirty_COW) but that is getting a little too in depth).

# Process Contents

## Memory Layout

![Address Space](http://flylib.com/books/2/830/1/html/2/images/09fig9_3.jpg)

When a process starts, it gets its own address space. Meaning that each process gets (for Memory
* **A Stack**. The Stack is the place where automatic variable and function call return addresses are stored. Every time a new variable is declared, the program moves the stack pointer down to reserve space for the variable. This segment of the stack is Writable but not executable. If the stack grows too far (meaning that it either grows beyond a preset boundary or intersects the heap) you will get a stackoverflow most likely resulting in a SEGFAULT or something similar. **The stack is statically allocated by default meaning that there is only a certain amount of space to which one can write**
* **A Heap**. The heap is an expanding region of memory. If you want to allocate a large object, it goes here. The heap starts at the top of the text segment and grows upward (meaning sometimes when you call `malloc` that it asks the operating system to push the heap boundary upward). This area is also Writable but not Executable. One can run out of heap memory if the system is constrained or if you run out of addresses (more common on a 32bit system).
* **A Data Segment** This contains all of your globals. This section starts at the end of the Text segment and is static in size because the amount of globals is known at compile time. This section is Writable but not Executable and there isn't anything else too fancy here.
* **A Text Segment**. This is, arguably, the most important section of the address. This is where all your code is stored. Since assembly compiles to 1's and 0's, this is where the 1's and 0's get stored. The program counter moves through this segment executing instructions and moving down the next instruction. It is important to note that this is the only Executable section of the code. If you try to change the code while it's running, most likely you will segfault (there are ways around it but just assume that it segfaults).
* Why doesn't it start at zero? It is outside the [scope](https://en.wikipedia.org/wiki/Address_space_layout_randomization) of this class but it is for security.

## File Descriptors

![Fds](https://drawings.jvns.ca/drawings/file-descriptors.svg)

As the little zine shows, the OS keeps track of the file descriptors and what they point to. We will see later that file descriptors need not point to actual files and the OS keeps track of them for you. Also, notice that between processes file descriptors may be reused but inside of a process they are unique.

File descriptors also have a notion of position. You can read a file on disk completely because the OS keeps track of the position in the file, and that belongs to your process as well.

## Security/Permissions

# Process Functions/Limitations (Bonus)

When you are reviewing for the final, you can come back and see that a process also has all of these things. The first time around -- it won't make much sense.

## Process ID (PID)

To keep track of all these processes, your operating system gives each process a number and that process is called the PID, process ID.

Processes could also contain
* Mappings
* State
* File Descriptors
* Permissions

<div align="center">
<a href="https://github.com/angrave/SystemProgramming/wiki/Forking%2C-Part-1%3A-Introduction">
Forking, Part 1: Introduction
</a>
</div>