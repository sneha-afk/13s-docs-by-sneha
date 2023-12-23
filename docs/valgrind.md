---
title: Valgrind
layout: default
nav_order: 6
has_toc: true
---

# `valgrind`
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Intro

**`valgrind`** is a utility to debug memory issues within programs. What good is a bucket of water if it leaks at the bottom? The same goes for your programs.

### Resources
* Official `valgrind` [quick start guide](https://valgrind.org/docs/manual/quick-start.html)
* [Comprehensive guide](https://web.stanford.edu/class/archive/cs/cs107/cs107.1174/guide_valgrind.html) @ Stanford

### Prereqs

Ensure `valgrind` is installed:

```bash
$ sudo apt-get install valgrind
```

If you are having issues with installing, consult the [troubleshooting page][TS: Installation].

---

## TL;DR

When you have a program called exec, you’d run it on terminal like:
```bash
$ ./exec
```

> Recall from [`bash`][bash Page] that `./` denotes something in the current directory.

To see the output of valgrind for this program, simply:
```bash
$ valgrind ./exec
```

{: .important}
`valgrind` runs on an executable, **not** a source `.c` file.

To get the details of all memory leaks, run it with the option `--leak-check=full`.
```bash
$ valgrind --leak-check=full ./exec
```

{: .note}
There are other options available for `valgrind`, but the leak check option is very helpful and should be used often.

Add the **`-gdwarf-4`** compiler flag to get more specific line numbers of erroneous accesses and memory allocations that are not being freed. 

{: .warning}
Do not submit a `Makefile` with this compiler flag (i.e, do not push this onto your repo, keep it local), as it makes the executable unnecessarily large and is only used for debugging purposes.

In a `Makefile`:
```make
CFLAGS = -Wall .... -gdwarf-4
```

In the terminal:

```bash
$ gcc -gdwarf-4 ... -o exec
```

{: .note}
Online you will see the -g flag being mentioned, which also accomplishes this, but there could be compatibility issues with the latest version of Ubuntu. Specifying the version with `-gdwarf-4` removes the headache.

{: .tip}
With Vim, you can enter the command `:set number` to get line numbers. Better yet, save this (and other settings for Vim) within `~/.vimrc` to retain across different Vim sessions.

Aim to see this message with no other errors or warning when possible.
```
All heap blocks were freed -- no leaks are possible
```

---

## Simple case

It makes more sense to explain `valgrind` with a continuing example.

I will be building upon a file called `sandbox.c` and its corresponding `Makefile`.

```c
// sandbox.c

#include <stdio.h>

int main() {
    int x = 1;
    printf("x = %d\n", x);
   
    return 0;
}
```

```make
# Makefile
CC = gcc
CFLAGS = -Wall

.PHONY = all clean

all: sandbox

sandbox: sandbox.o
    $(CC) $(CFLAGS) -o $@ $^

%.o: %.c
    $(CC) $(CFLAGS) -c $<

clean:
    rm -rf *.o sandbox

```

Let’s run `sandbox` right now with `valgrind`:
```bash
$ make
gcc -Wall -c sandbox.c
gcc -Wall -o sandbox sandbox.o

$ valgrind ./sandbox
==6917== Memcheck, a memory error detector
==6917== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==6917== Using Valgrind-3.18.1 and LibVEX; rerun with -h for copyright info
==6917== Command: ./sandbox
==6917== 
x = 1
==6917== 
==6917== HEAP SUMMARY:
==6917==     in use at exit: 0 bytes in 0 blocks
==6917==   total heap usage: 1 allocs, 1 frees, 1,024 bytes allocated
==6917== 
==6917== All heap blocks were freed -- no leaks are possible
==6917== 
==6917== For lists of detected and suppressed errors, rerun with: -s
==6917== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

{: .note}
“6917” refers to the process ID of the executable. This number will change and be random, don’t worry about it. The output of your program is everything between `==#==` lines, in this case being `x = 1`.

That’s great, no heap memory leaks! In a program with just one static variable. The 1024 heap bytes used probably originates from the internals of printf, but the system can clean itself up in many cases.

Now to add some dynamic memory:
```c
// sandbox.c

#include <stdio.h>
#include <stdlib.h>

int main() {
    int x = 1;
    printf("x = %d\n", x);

    // Three integers
    int *arr = (int *) malloc(sizeof(int) * 3);
    arr[0] = 0;
    arr[1] = 1;
    arr[2] = 2;
   
    return 0;
}
```

What does valgrind say? After recompiling…
```
==14575== Memcheck, a memory error detector
==14575== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==14575== Using Valgrind-3.18.1 and LibVEX; rerun with -h for copyright info
==14575== Command: ./sandbox
==14575== 
x = 1
==14575== 
==14575== HEAP SUMMARY:
==14575==     in use at exit: 12 bytes in 1 blocks
==14575==   total heap usage: 2 allocs, 1 frees, 1,036 bytes allocated
==14575== 
==14575== LEAK SUMMARY:
==14575==    definitely lost: 12 bytes in 1 blocks
==14575==    indirectly lost: 0 bytes in 0 blocks
==14575==      possibly lost: 0 bytes in 0 blocks
==14575==    still reachable: 0 bytes in 0 blocks
==14575==         suppressed: 0 bytes in 0 blocks
==14575== Rerun with --leak-check=full to see details of leaked memory
==14575== 
==14575== For lists of detected and suppressed errors, rerun with: -s
==14575== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

What went wrong? Some heap memory hasn’t been freed as seen by the “in use at exit” along with the discrepancy between 2 allocations versus only 1 free. In this smaller example, you can see how those 12 bytes in one block are because the one “block” is an array of 3 integers (4 bytes each).

Rectify it by calling `free()` on the pointer to that array.
```c
// sandbox.c

#include <stdio.h>
#include <stdlib.h>

int main() {
    int x = 1;
    printf("x = %d\n", x);

    // Three integers
    int *arr = (int *) malloc(sizeof(int) * 3);
    arr[0] = 0;
    arr[1] = 1;
    arr[2] = 2;
   
    free(arr);
    return 0;
}
```

```
==15298== Memcheck, a memory error detector
==15298== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==15298== Using Valgrind-3.18.1 and LibVEX; rerun with -h for copyright info
==15298== Command: ./sandbox
==15298== 
x = 1
==15298== 
==15298== HEAP SUMMARY:
==15298==     in use at exit: 0 bytes in 0 blocks
==15298==   total heap usage: 2 allocs, 2 frees, 1,036 bytes allocated
==15298== 
==15298== All heap blocks were freed -- no leaks are possible
==15298== 
==15298== For lists of detected and suppressed errors, rerun with: -s
==15298== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

Back to peace.

A good practice after freeing a block of memory is to set its pointer to `NULL` as it is easier to debug an illegal `NULL` dereference than something going wrong with heap. Additionally, `free(NULL);` does not trigger an error!
```c
    free(arr);
    arr = NULL;
```

---

## Common errors

### Invalid read/write of size X

> Having this error usually accompanies a segmentation fault, which means it’s probably the most common error you will find.

This occurs when attempting to reference or use invalid memory such as a NULL pointer, exceeding the bounds of an array, or trying to access a block of memory you previously freed. 

{: .tip}
Before doing an operation upon a pointer, always think about the chance it could be NULL from any previous logic.

**Example 1**
```c
free(a);

// Invalid read: freed block cannot be dereferenced/read from
printf("a = %d\n", *a);

// Invalid write: freed block cannot be assigned
a[0] = 1;
```

**Example 2**
```c
// Somewhere else in the program
typedef struct {
    int num;
} A;

// =====================
A* obj = malloc(sizeof(A));

obj = NULL;

// Invalid write: NULL pointer cannot be dereferenced
obj->num = 2;
```

**Example 3**
```c
int *arr = (int *) malloc(sizeof(int) * 3);
arr[0] = 0;
arr[1] = 1;
arr[2] = 2;

// Invalid read: the block pointed to by arr was not allocated to contain a 4th integer
printf("arr[3] = %d\n", arr[3]);
```


### Use of uninitialised value/ Conditional jump or move depends on uninitialised value(s)

If you have been to my sessions, you’d know my catch-all phrase, “declaration vs initialization.” That is essentially what this error warns you about, the usage of uninitialized variables.

The program may try to rectify this by assigning a default value for a type (such as a declared integer being defaulted to 0), but this should not be taken for granted nor is it good practice. 

{: .tip}
Initialize anything that needs to be used as if it has a value.

**Example 1**
```c
int f;

// Usually will print "f = 0", but still not appreciated
printf("f = %d\n", f);
```

### Invalid free() / delete / delete[] / realloc()

This occurs when you try to `free()` a block of memory that has already been freed previously. 

It is encouraged to set freed pointers to NULL because free(NULL); will not trigger this issue and has no ill effects.[^1]

**Example 1**
```c
int *a = (int *) malloc(sizeof(int));
free(a);

// Invalid free(), the block pointed to by a has already been freed
free(a);


// ==================
// Solution to prevent this:
int *a = (int *) malloc(sizeof(int));
free(a);
a = NULL;

free(a); // no problem since a = NULL
```

---

[TS: Installation]: troubleshooting.html/#installation

[bash Page]: bash.html

[^1]: [Documentation](https://pubs.opengroup.org/onlinepubs/009604499/functions/free.html) for `free()`
