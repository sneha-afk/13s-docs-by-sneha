---
title: "Macros vs. const"
parent: "About C"
layout: default
nav_order: 2
has_toc: true
last_modified_date: 2024-01-03 at 2:35 PM
---

# Macros vs. `const`
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Definitions

**Macros** are used to nickname string expression to a reusable name.
* No type-checking, expanded by substitution by the preprocessor

**`const`** is a type qualifier for variables
* Prevents a variable from being modified after its first initialization
* Often specified for arguments to enforce a "read-only" policy

---

## Examples

```c
#define SIZE1 10
const int SIZE2 = 10;
```

`SIZE1` can easily be “a” *without* complaints (until you actually tried to use it as an integer later), since there is no type associated with macros.

```c
// const to avoid unintentional changes to original
Obj *clone_obj(const Obj *original) {
    Obj *new = ...
    return new;
}
```

---

## Tricky macro question

What is the value of `r` and `b` by the end of this program?
```c
#define MAX(x, y) ((x) > (y) ? (x) : (y))

int main() {
    int a = 5, b = 8;
    int r = MAX(a++, b--);
    return 0;
}
```

<details markdown="block">
  <summary>
    Answer (click here)
  </summary>
  {: .text-delta }

r = 7, b = 6
</details>

### Explanation

The preprocessor will expand `r` to:
```c
r = (a++ > b-- ? a++ : b--);
```

{: .note}
> Refresher on ternary operators: `(condition) ? return if true : return if false`
>
> Since the increments/decrements are AFTER the variable name, this means “use the current value, and then increment/decrement”
>
> Read more: K&R 2.8 Increment and Decrement Operators, page 46

Substituting the values of a and b (technically the right hand side too, but I will ignore those for now):
```c
r = ((5)++ > (8)-- ? a++ : b--);
```

Since 5 is not > than 8, the return-if-false expression is returned.

Keep in mind that a needs to be incremented, and b needs to be decremented, so after the comparison, a = 6 and b = 7.

```c
r = b--;
```

The post-decrement rule is applied again, so first r is set to the current value of b, which is 7, and then b is decremented to 6.

---

## References
1. K&R, section 4.11.2: Macro Substitution (p.89 of the physical book)
2. K&R section A8.2 Type Specifiers (p. 211 of the physical book)
