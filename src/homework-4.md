# Homework 4

Ishan Pranav

October 30, 2024

Professor Yang Tang

CSCI 202 Operating Systems

## Question 1

Alice has written the following code, which contains a common bug in C
programming.

```c
#include <stdio.h> 
#include <stdlib.h> 

struct box 
{
    int id;
    struct box* inner_box;
};

void insert_box(struct box* outer, struct box inner) 
{
    printf("insert box: placing id %d inside id %d\n", inner.id, outer->id);

    outer->inner_box = &inner; // line (a)
}

void print_box(struct box* first, int level) 
{
    int i;
    
    if (!first)
    {
        return;
    }
    
    for (i = 0; i < level; ++i) 
    {
        printf("- ");
    }
    
    printf("id: %d\n", first->id);
    print_box(first->inner_box, level + 1);
}

int main() 
{
    struct box box1 = { .id = 37, .inner_box = NULL };
    struct box box2 = { .id = 12, .inner_box = NULL };
    struct box box3 = { .id = 19, .inner_box = NULL };
    
    insert_box(&box1, box2);
    insert_box(&box2, box3);
    print_box(&box1, 0);
    
    return 0;
}
```

### Question 1 Part 1

> Describe at a high level what this code is intended to do. What output did
> Alice expect the program to generate?

The code simulates 3 boxes: 1, 2, and 3, and prints the state of the boxes. Box
2 is inside of Box 1 and Box 3 inside of Box 2.

### Question 1 Part 2

> What happens when the program is run?

When the program is run, the process recurses infinitely: Box 1 contains Box 3,
which contains Box 1.

### Question 1 Part 3

> What is the error in Alice’s reasoning?

On line (a), Alice assigns `&inner` into `outer->inner_box`. Here, `&inner` is
the address of a function argument, which is a local variable. Once the
`insert_box` function terminates, its stack frame is deallocated, and the
reference to `inner` is broken. As a result, `outer->inner_box` is an invalid
pointer.

Later, the `print_box` function dereferences `outer->inner_box`, which invokes
undefined behavior.

Instead, Alice should receive the pointer as an argument to `insert_box`.

### Question 1 Part 4

> Change this program to produce the intended output.

We can redefine and reimplement `insert_box`:

```c
void insert_box(struct box* outer, struct box* inner) 
{
    printf("insert box: placing id %d inside id %d\n", inner->id, outer->id);

    outer->inner_box = inner;
}
```

Then reimplmenet `main` to fix the issue:

```c
int main() 
{
    struct box box1 = { .id = 37, .inner_box = NULL };
    struct box box2 = { .id = 12, .inner_box = NULL };
    struct box box3 = { .id = 19, .inner_box = NULL };
    
    insert_box(&box1, &box2);
    insert_box(&box2, &box3);
    print_box(&box1, 0);
    
    return 0;
}
```

## Question 2

> When a computer is being developed, it is usually first simulated by a program
> that runs one instruction at a time. Even multiprocessors are simulated
> strictly sequentially like this. Is it possible for a race condition to occur
> when there are no simultaneous events like this?

Yes, it is possible for a race condition on a sequential processor. A single
processor does not guarantee a single process. For example, there may be two
logical processes executing on the sequential computer, with context switching
between the two processes. In this case, each process may depend on and modify
shared state, causing a race condition is possible.

## Question 3

> The producer-consumer problem can be extended to a system with multiple
> producers and consumers that write (or read) to (from) one shared buffer.
> Assume that each producer and consumer runs in its own process. Will the
> solution presented using semaphores, work for this system?
