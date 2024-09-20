# Homework 2

Ishan Pranav

October 2, 2024

Professor Yang Tang

CSCI 202 Operating Systems

## Question 1

> Alice has the impression that “arrays are the same as pointers” in C, so she
> writes the following program to test if that’s really the case.

```c
#include <stdio.h>

size_t f(const char *c) 
{ 
    return sizeof(c); 
}

int main()
{
    const char a[] = "Hello, CS202!"; 
    const char *b = "Hello, CS202!"; 

    printf("size of a is %lu\n", sizeof(a)); // (a)
    printf("size of b is %lu\n", sizeof(b)); // (b)
    printf("f(a) returns %lu\n", f(a));      // (c)
    printf("f(b) returns %lu\n", f(b));      // (d)
}
```

> Alice expects to see all four lines print 14. However, when she runs this
> program, she sees different results.

### Question 1 Part 1

> Which line(s) prints 14? Why?

Only line (a) prints 14. Although arrays "decay" into pointers in C, they are
actually treated differently from a language perspective. Since `a` is a
stack-allocated array, the `sizeof` operator returns its size in bytes: 14.

### Question 1 Part 2

> What do other line(s) print? Why?

Meanwhile, `b` is written as a pointer, not an array, so the `sizeof` operator
returns the size of the pointer: for example, 8 on a 64-bit architecture. When
`a` is passed to `f`, it "decays" into a pointer, so it too has size 8. Of
course, when `b` is passed to `f` the result is 8 as well.

### Question 1 Part 3

> In fact, `sizeof` shouldn't be used to get the length of a string at all. Why?

The `sizeof` operator should not be used to obtain the length of a string
because strings may be stack allocated (like `a`), heap allocated, or stored as
data (like `b`).

Instead, the `strlen` function computes the length of the string up to but not
including the zero terminator.

## Question 2

> Bob just learned the `exec` family of system calls in his CS202 class, and he
> wants to try it out himself. So he writes the following program.

```c
#include <stdio.h> 
#include <unistd.h> 

int main() 
{
    printf("one");                                // (a)
    execl("/bin/echo", "/bin/echo", "two", NULL); // (b)
    printf("three"); 
}
```

> Bob expects to see “one” and “two” printed but not “three”. However, when he
> runs this program on the CIMS compute servers, only “two” is printed. He
> examines his screen carefully with his magnifying glass but still cannot find
> any trace of “one”. Bob gets so confused, so he turns to you for help.

### Question 2 Part 1

> Can you explain to Bob what’s happening here? Can you propose a way to make
> both “one” and “two” printed?

By default, the standard output stream is buffered. Bob is using `printf`, which
writes to the standard output stream. After the call the call to
`printf("one")`, the string `"one"` has been written to the buffer, but not
necessarily to the underlying output device.

The call to `execl` replaces the current program and clears the existing process
memory, including the buffer. The call to `execl` prints `"two"`. The remaining
code does not execute because once `execl` succeeds, it does not restore the old
program.

To print both `"one"` and `"two"`, Bob can ensure that the buffer is written to
the standard output stream by inserting the line `fflush(stdout);` between
lines (a) and (b). Here, `fflush` "flushes" the standard output buffer.

### Question 2 Part 2

> Can you propose a way to make all “one”, “two”, and “three” printed?

Bob can use `fork` to create a new process. The child process can make the call
to `execl`, while the parent process can call `wait` to wait for the child
process to terminate before printing `"three"`.

```c
#include <sys/wait.h>
#include <stdio.h> 
#include <unistd.h> 

int main() 
{
    printf("one");
    fflush(stdout);

    pid_t pid = fork();

    if (!pid)
    {
        execl("/bin/echo", "/bin/echo", "two", NULL);
    }
    else if (pid > 0)
    {
        wait(NULL);
        printf("three");
    }
}
```

## Question 3

> For each of the following system calls, give a condition that causes it to
> fail: `fork`, `exec`, and `wait`.

- The `fork` function fails when attempting to execeed the system's thread count
limit.
- The `exec*` functions fail when the path or file name argument refers to a
file that does not exist.
- The `wait` function fails when the calling process does not have any
unwaited-for children.

## Question 4

> To a programmer, a system call looks like any other call to a library
> function. Is it important that a programmer know which library functions
> result in system calls? Under what circumstances and why?

No, generally, it is not important to know which library functions result in
system calls. Focusing on this aspect breaks the abstraction of the API and
requires the programmer to rely on assumptions about the underlying
implementation.

However, in high-performance applications or systems programming tasks, it is
important to identify system calls and the library functions that invoke them
because system calls result in _trap_ and _return-from-trap_ instructions that
switch between user and kernel mode. These instructions are expensive and can
damage performance if called often. As a result, applications and libraries seek
to reduce system calls, by using buffers for example.

## References

- [The C99 standard, 2007 committee draft](https://port70.net/~nsz/c/c99/n1256.html)
- Linux manual pages
  - [exec(3) - execute a file](https://www.man7.org/linux/man-pages/man3/exec.3.html)
  - [fork(2) - create a child process](https://www.man7.org/linux/man-pages/man2/fork.2.html)
  - [wait(2) - wait for process to change state](https://www.man7.org/linux/man-pages/man2/wait.2.html)
