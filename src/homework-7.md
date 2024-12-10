# Homework 7

Ishan Pranav

December 11, 2024

Professor Yang Tang

CSCI 202 Operating Systems

## Question 1

Alice has written the following code to play with a process's memory.

```c
int main()
{ 
    char a = 'a'; 
    char* b = &a; 
    char* c = "c"; 
    char d[] = "d"; 
    char* e = d; 

    a = 'A';     // (a) 
    b[0] = 'B';  // (b) 
    c[0] = 'C';  // (c) 
    d[0] = 'D';  // (d) 
    e[0] = 'E';  // (e) 
}
```

> For each line (a) through (e), will it cause a segmentation fault? Why?

Line (a) does not cause a segmentation violation. It reassigns the character
variable `a` from `'a'` to `'A'`.

Line (b) does not cause a segmentation violation. It reassigns the character
variable `a` from `'A'` to `'B'` via one level of indirection.

Line (c) may cause a segmentation violation. It attempts to assign a character
with in a string literal, which is undefined behavior. String literals are often
stored in the read-only data segment of a program and should not be modified.

Line (d) does not cause a segmentation violation. It reassigns the first
character in the array `D` from `'d'` to `'D'`.

Line (e) does not cause a segmentation violation. It is equivalent to line (d)
via pointer aliasing.

## Question 2

> Name one advantage of hard links over symbolic links and one advantage of
> symbolic links over hard links.

One advantage of hard links is that they remain valid even after the referenced
file is deleted.

One advantage of symbolic links is that they can link to files across drives and
file systems.

## Question 3

> For an external USB drive attached to a computer, which is more suitable: a
> write-through cache or a write-back cache?

A write-through cache is more suitable for an external USB drive attached to a
computer since it guarantees that the data are written to the disk in the event
that it is removed prematurely.

## Question 4

Consider a RAID level 5 organization comprising five disks, with the parity for
sets of four blocks on four disks stored on the fifth disk. How many blocks are
accessed in order to perform the following?

### Question 4 Part 1

> A write of one block of data.

A write of 1 block requires 4 blocks to be accessed under RAID 5.

### Question 4 Part 2

> A write of seven continuous blocks of data beginning at a four-block boundary.

Under RAID 5, a write of seven contiguous blocks of data beginning at a
four-block boundary requires 18 blocks to be accessed.
