---
title: "Pointers"
parent: "About C"
layout: default
nav_order: 4
has_toc: true
last_modified_date: 2024-01-09 at 11:12 PM
---

# Pointers
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Definition

> "A **pointer** is a variable that contains the address of a variable." (p. 93)

The name "pointer" is used to provide an easy visualization: a pointer "points" to a spot in memory by storing the address of it (usually expressed in terms of hex digits).

{: .warning}
This can be, and is, the most difficult aspect of C. Tread carefully.

## Usage

To obtain a pointer, you use the **address-of** operator `&` on the left hand side of the variable.
```c
int a = 1;
int *ptr_a = &a;
```

The `int *` indicates a pointer to an integer.

To obtain the *thing stored at that location and is pointed to by the pointer* use the **indirection** (colliqually a "deference") operator `*` on the left hand side of the variable.
```c
int a = 1;
int *ptr_a = &a;
printf("a = %d\n", *ptr_a); // Get a and print it out
```

{: .note}
`*` means different operations depending if it's on the left or right hand side of an equal sign, as seen with `int *ptr_a` to declare the pointer, and `*ptr_a` to deference it.

Altogether:
```c
// obj is a pointer to something of type A
A* obj;
// the * can be attached to the A or obj
A *obj;

// get the value of whatever obj is pointing to
A something = *obj + 1
```

## Pointer arithmetic

Since pointers are numbers in the system, some arithmetic can be done on them

* ptr + num = ptr
* ptr - num = ptr
* ptr - ptr = distance between the two pointers
* ~~ptr + ptr~~ Undefined, cannot subtract two pointers
* ptr++, ptr--
    * Increment/decrement pointer by the size of the type its pointing to (i.e, if it’s an integer array, change by 4 bytes)
* Comparison operators (>, <, >=, <=, ==, !=) all work between two pointers

The relationship between pointers and arrays is evident in the following equation for accessing an array element at index `i`.
```c
*(arr + i) = arr[i]
```


## Examples

### Pointer-ception

This example shows how pointers can continue on endlessly, since storing a pointer a memory means *the pointer has it's own address!*
```c
int a = 1;
int *b = &a;
int **c = &b;
// internally: c -> b -> a = 1

// d == e == f!
int d = a + 1;
int e = *b + 1;
int f = **c + 1;
```

### Scopes, heaps, stackframes, yikes!

Pointers are frequently used in tricky scoping situations like below. If you do not remember what a stackframe is, refer to CSE12.

```c
#include <stdio.h>
#include <stdlib.h>

// global scope, anyone below this line in this file
// can see these two
int global1 = 1;
int global2;

int foo(int* int_arr) {
    // local scope of foo, a separate *stackframe* from main
    // stackframe: section of the program's stack
    // but sharing the same heap space (same program execution)

    // main() can't see this
    int foo_local1;
}

int main() {
    // local scope, anyone below this in main() can see it
    // foo() can't see this
    int main_local1;

    // i is not visible in the scope of main() or globally,
    // only inside the scope of the for loop
    for (int i = 0; i < 10; i++) {
        printf("%d\n", i);
    }

    // local to main, but is in heap so others can access it
    // if they are given the pointer to the heap space
    int *arr = calloc(10, sizeof(int));
    foo(arr);

    // give foo() a static array, need to give a pointer
    // foo() can't access main()'s stackframe, so need a pointer
    int static_arr[] = {1, 2, 3};
    foo(&static_arr);

    // static_arr already taken care of by system, stackframes
    // are not my problem, only my heap actions
    free(arr);
    return 0;
}
```

### Who needs addresses?

```c
char* str = "hello";
printf("Char: %c, Add.: %ld\n", *str, str);
printf("Char: %c, Add.: %ld\n", *(str + 1), (str + 1));
    
char* str_incr = str + 2;
printf("Char: %c, Add.: %ld\n", *str_incr, str_incr);
printf("Char: %c, Add.: %ld\n", *(str_incr + 1), (str_incr + 1));
    
int nums[] = {1, 2, 3};
printf("Inte: %d, Add.: %ld\n", *nums, nums);
printf("Inte: %d, Add.: %ld\n", *(nums + 1), (nums + 1));
printf("Inte: %d, Add.: %ld\n", nums[1], &(nums[1]));
```

The output of this program is:
```
Output:
Char: h, Add.: 4202500
Char: e, Add.: 4202501
Char: l, Add.: 4202502
Char: l, Add.: 4202503
Inte: 1, Add.: 140729590748324
Inte: 2, Add.: 140729590748328
Inte: 2, Add.: 140729590748328
```

Notice the difference in addresses between characters and integers. Also notice the one byte difference in addresses for the characters versus the four byte difference for the integers.

Addresses are random, may be different if you run this!

## References
1. K&R, Chapter 5: Pointers and Arrays (page 93)
2. A7.4.2 Address Operator (p.203)
3. A7.4.3 Indirection Operator (p.204)
