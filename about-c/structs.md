---
title: "structs"
parent: "About C"
layout: default
nav_order: 3
has_toc: true
last_modified_date: 2024-01-10 at 12:11 PM
---

# `struct`s
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Definitions

### `struct`
A `struct` is somewhat analogous to a class in Java or a dataclass in Python.

```c
// Declaration of a struct called student
struct student {
    int id;
    int slug_points;
}

// Analogous to saying "int Sammy", the type here is a "struct student"
struct student Sammy;
Sammy.id = 1234;
Sammy.slug_points = 0; // oh no

// Analogous to "int Bob = 1", intialize in order of the fields declared in the struct definition
struct student Bob = { 5678, 1 };
```

> "A member of a particular structure is referred to in an expression bya construction of the form: `structure-name.member`" (p.128)

> "If `p` is a **pointer** to a structure, then [accessing a member would be:] `p->member-of-structure`" (p.131)

### `typedef`
`typedef` is used to declare new names for datatypes.

```c
// Alias type A to be called B
typedef A B
```

{: .important}
There is no "creating" new datatypes within C, the closest concept to this would be declaring a new `struct` that is `typedef`'ed to an identifier.

## Example

This example makes a new `struct` called `AObj` and then `typedef`'s a pointer to the struct to be called `A` for ease of use.

{: .note}
This is a very common pattern within C programming, become comfortable with it.

```c
// Typedefing a pointer to this struct to be called A
// i.e, instead of saying "struct AObj*", can refer to it as A
typedef struct AObj* A;

typedef struct AObj {
    int a;
    int b;
    A another;
} AObj; // shortcutting "struct AObj" to be called just AObj

// When using the direct struct, access fields with .
AObj obj;
obj.a = 1;

// When using a pointer to a struct, use ->
A ptr_obj;
ptr_obj->a = 1;
```

The first `typedef` might be confusing, how can you define a pointer to `struct AObj` before you define said `struct`? Note that this *only* works for pointers to `struct`s, since C trusts that you will eventually define `struct AObj` before you use `A`.

## Bigger example

This example's purpose is two-fold: showcase the call-by-value aspect of C, and showcase using `struct`s within your programs.

```c
#include <stdio.h>

struct AObj {
    int a;
    int b;
    int c;
};

// Make it easier to refer to AObj
// type: struct AObj, new name: AObj
typedef struct AObj AObj;
typedef AObj* A;

// Shortcut of declaring a struct type, and making its name easier to reference
typedef struct BObj {
    A aobj;
    int a;
} BObj;
typedef BObj* B;

void another(A aobj) {
    // Structures can only be copied or moved around using pointers
    // Copying can be inefficient, generally appreciated to use pointers
    printf("aobj->b = %d\n\n", aobj->b);
}

int main() {
    struct AObj first;
    AObj second = {1, 2, 3};

    // Accessing from a struct, the . operator (p.128)
    printf("Field b of second struct: second.b = %d\n\n", second.b);

    // Pointer to a struct, typedef (p.221)
    // pobj->member is a shortcut from having to do (*obj).member (p.131)
    A third = &second;
    printf("third points to the second struct: third->b = %d\n\n", third->b);

    // Using pointers and/or address-of & to move structs around in programs
    printf("another(&second)\n\t");
    another(&second);

    printf("another(third)\n\t");
    another(third);

    // structs within another structs (p.131)
    BObj fourth = {third, 0};
    B fifth = &fourth;

    printf("struct-ception: . and -> are left-to-right associative\n\t");
    printf("fourth.aobj->b = fifth->aobj->b = %d\n", fifth->aobj->b);

    return 0;
}
```

The output of this program is:
```
Field b of second struct: second.b = 2

third points to the second struct: third->b = 2

another(&second)
	aobj->b = 2

another(third)
	aobj->b = 2

struct-ception: . and -> are left-to-right associative
	fourth.aobj->b = fifth->aobj->b = 2
```


## References
1. K&R, Chapter 6: Structures (page 127)
    * 6.1 - Basics of Structures
    * 6.2 - Structures and Fucntions
    * 6.4 - Pointers to Structures
    * 6.7 - Typedef
