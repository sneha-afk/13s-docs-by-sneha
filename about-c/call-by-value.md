---
title: "Call-By-Value"
parent: "About C"
layout: default
nav_order: 1
has_toc: true
last_modified_date: 2024-01-03 at 1:36 PM
---

# Call-By-Value
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Example
Review an important concept in programming: parameters vs. arguments
* A **parameter** is the applied name of an argument in a function/scope. That is, a parameter is the name that is assumed for an argument.
* **Arguments** contain actual values and are passed into a program, function, etc.

In the following example, `main()`’s variable apple is an argument into `foo()`’s apple parameter.
> Remember scopes! These two apple’s are not the same.

```c
int foo(int apple) {
    int carrot = apple + 1;
    return carrot;
}

int main() {
    int apple = 5;
    int banana = foo(apple);
   
    // %d is a format specifier for integers, K&R p.244
    printf("Value of b = %d", banana);

    // 0 indicates a successful run of this program
    return 0;
}
```

`main()`’s `apple` has a value of 5, which is copied into the parameter `apple` in `foo()`.

---

## Definition
> “The main distinction is that in C the called function cannot directly alter a variable in the calling function; it can only alter its private, temporary copy.” (p.27, K&R)

In simple terms, **call-by-value means that the values of arguments are copied into the parameters, while the original argument remains untouched within the original scope.**

---

## “But pointers!” 

No, **everything** in C is call-by-value. No exceptions.

Pointers are simply integers that are also copied, the contents they point may be altered, but the pointer itself is never changed.

Pointers simulate "call-by-reference" for the object being pointed to, but in reality the address of a pointer is copied into arguments so this is still call-by-value.

---

## References
1. K&R, section 1.8: Arguments - Call by Value (p.27 of the physical book)
