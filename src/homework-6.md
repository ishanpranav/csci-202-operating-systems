# Homework 6

Ishan Pranav

November 27, 2024

Professor Yang Tang

CSCI 202 Operating Systems

## Question 1

> Alice is sick this week. Bob has written the following code to examine some
> binary data.

```c
#include <stdio.h>

typedef struct Record
{
    unsigned char v1[3]; // "Bob"
    unsigned int v2;     // 76543210
} Record;

int main()
{
    unsigned char a[] = { 'B', 'o', 'b', 0x76, 0x54, 0x32, 0x10, 0 };
    Record* b = (Record*)a;
    
    printf("%s\n", b->v1);
    printf("%08x\n", b->v2);
}
```

> Bob thinks that the first line should print “Bob” and the second line
> “76543210”, but he is wrong. What are actually printed out? Why?

One possible output is:

```sh
BobvT2
00103254
```

Bob is attempting to print the byte array `v1` as a zero-terminated string. This
means the string continues until the first instance of the null byte.

Also, Bob wants to cast a raw byte array to a structure of type `Record`,
although he has not considered the impact of structure alignment. This means
that the unsigned integer `v2` is misaligned and the incorrect output is
possible. The exact result will be implementation-defined.

## Question 2

> In Linux, random access is done by the `lseek` system call, which moves the
> “current position” pointer associated with a file to a given byte in the file.
> Propose an alternative way to do random access without having this system
> call. Which way is better? Why?

An alternative way to support random access without the `lseek` system call is
to treat all files as memory-mapped. Although this may come with more overhead
and take up part of the address space, it would allow a unified memory model
in which the hard disk drive and main memory are semantically equivalent.

## Question 3

### Question 3 Part A

> Describe the effects of a corrupted data block for a given file for a
> linked-list-based file system implementation.

In a linked-list-based implementation, if a data block is corrupted it may be
impossible to retrieve the pointer to the next block. This means the entire file
is compromised. It may be impossible to detect this issue, in which case
self-loops and other types of invalid pointers are psosible. Following, reading
to, and writing from these pointers may further damage the system.

### Question 3 Part B

> Describe the effects of a corrupted data block for a given file for a
> table-based file system implementation.

In a table-based implementation, the file allocation table remains intact even
if a data block is corrupted. This means that, although the data in the
corrupted block are lost, the overall file system remains functional. Also, the
remaining blocks of the file can be accessed without issue, minimizing the
overall data loss. A table-based implementation is thus more secure.
