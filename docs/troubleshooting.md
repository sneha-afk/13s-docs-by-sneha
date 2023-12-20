---
title: Troubleshooting
layout: default
nav_order: 2
has_toc: true
---

# Troubleshooting

Common issues plague the early stage of learning C. Usually, nothing about C itself. Here are common solutions with sources when needed.

{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Git

### Author identity unknown

This error is displayed similar to:

```
Author identity unknown

 *** Please tell me who you are.

Run
    git config --global user.email "you@example.com"
    git config --global user.ename "Your Name"

to set your account's default identity.
Omit --global to set the identity only to this repository.
```

Git commits must be signed with a name and email.
* Set your name and email. Do as it said, i.e:

```bash
$ git config --global user.email "slug@ucsc.edu"
$ git config --global user.name "Sammy Slug"
```

* You can also configure the identity of a repository separately:

```bash
$ git config --global user.email mine@personal.com
$ git config --global user.name sluggo_wuggo

# Within an class's repo
$ git config --local user.email slug@ucsc.edu
$ git config --local user.name “Sammy Slug”
```

{: .note}
In most terminal operations, quotation marks are optional with single-tokens, but is necessary when tokens are space separated like a `"FirstName Last Name"`

## `make`

### "Where is my executable!"

`make` stops when the compiler finds a compilation error, so the final executable cannot be made. Fix all compilation errors and try once more.

{: .note }
Copilation *warnings* are usually excused by the compiler, but in general warnings should also be dealt with as much as possible.
