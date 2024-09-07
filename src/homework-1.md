# Homework 1

Ishan Pranav

September 18, 2024

Professor Yang Tang

CSCI 202 Operating Systems

## Question 1

> Alice is writing a simple program to practice the use of pointers. She uses
> the function add() to calculate the sum of two integers. Instead of returning
> the value, the function returns a pointer to the result.

```c
#include <stdio.h> 

int *add(int a, int b) 
{ 
    int c = a + b; 
    int *d = &c; 
    return d; 
} 

int main() 
{ 
    int *result = add(1, 2);  
    
    printf("result = %d\n", *result); 
    printf("result = %d\n", *result); 
}
```

> Surprisingly, Alice notices that if she prints the result for the second time,
> the result is wrong! So she turns to you for help.

### Question 1 Part 1

> Can you explain what’s happening here?

Alice is returning a pointer to a temporary stack-allocated value across
function calls. This is a problem because it results in undefined behavior.

### Question 1 Part 2

> Alice insists that `add` should return a pointer. Can you propose a way to fix
> it?

If Alice wants to return a pointer to a mutable object that contains the result,
she should first dynamically allocate the result on the heap and return a
pointer to the heap-allocated object. For example, if she called `malloc` and
returned a pointer to the allocated memory, the program would work as expected.

## Question 2

> Bob is playing with the UNIX/Linux shell, and he is confused about the
> following commands...

> Can you explain to Bob what each command does? What would happen if Bob
> executes these commands in order?

```sh
echo 'echo cat' | cat 
```

The `echo` statements writes a string to an output stream. The `|` (pipe)
symbol "pipes" the output into the following instruction. The `cat`
instruction concatenates an input stream with the standard output stream.

```sh
echo 'echo cat' > cat 
```

The `>` symbol writes the output into the following file path. Here `cat` is the
name of the file that contains the text "echo cat."

```sh
echo 'echo cat' >> cat
```

The `>>` symbol appends the output into the following file path. The `cat` file
now contains the text "echo cat" on the first line followed by "echo cat" on the
second line.

```sh
echo `echo cat` | cat
```

The `` ` `` symbols cause the `echo cat` instruction enclosed within them to be
executed first, and the output of that instruction is piped into the `cat`
instructions. Thus, the string "cat" is written to the standard output stream.

```sh
cat < cat
```

The `<` symbol reads the file and writes its output into the preceding `cat`
instruction. The contents of the cat file ("echo cat" on the first line and
"echo cat" on the second line) are written to the standard output stream.

## Question 3

> What is the difference between kernel and user mode? Explain how having two
> distinct modes aids in designing an operating system.

Compared to user mode, kernel mode has advanced privileges, such as unrestricted
hardware access. Having two distinct modes aids in designing an operating system
in two ways. First, it provides a layer of information hiding and abstraction
that makes application code (which executes in user mode) much simpler and more
maintainable. Second, it provides safety and security, protecting the hardware
from user error and malicious programs that execute in user mode.

## Question 4

> What is a trap instruction? Explain its use in operating systems.

A trap instruction switches from user mode to kernel mode. In operating systems,
a trap causes a software interrupt that is handled by the kernel.

## Question 5

> Instructions related to accessing I/O devices are typically privileged
> instructions, that is, they can be executed in kernel mode but not in user
> mode. Give a reason why these instructions are privileged.

Instructions relating to accessing input and ouput devices are typically
privileged instructions because they involve accessing hardware directly. For
example, these include the monitor, keyboard, disk, external storage, printer,
and other input and output devices. Requiring these devices to be accessed from
user mode would force third-party applications to adapt their user code to many
different hardware providers, which would be redundant and error-prone. Instead,
device manufacturers, who know the most about the devices they produce, create
tailored device drivers that execute in kernel mode. This protects users from
the challenges of interoperability.

## References

- [Backticks in Linux command](https://unix.stackexchange.com/questions/27428/what-does-backquote-backtick-mean-in-commands)
