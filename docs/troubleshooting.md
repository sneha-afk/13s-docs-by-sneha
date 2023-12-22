---
title: Troubleshooting
layout: default
nav_order: 2
has_toc: true
---

# Troubleshooting
{: .no_toc }

Common issues plague the early stages of learning C. Usually, nothing about C itself. Here are common solutions with sources when needed.

## Table of contents
{: .no_toc }

1. TOC
{:toc}

## General

### Installation

#### "`username` is not in the sudoers file"

If you are having issues with installing, such as an error saying:
```
<username> is not in the sudoers file. This incident will be reported
```

Then run the following commands (Source: Stack Overflow):
```bash
$ su
$ password: <password of your VM>
$ usermod -aG sudo <username>
```

Restart your VM, and try the installation again.

## Git

### Author identity unknown

```
Author identity unknown

 *** Please tell me who you are.

Run
    git config --global user.email "you@example.com"
    git config --global user.ename "Your Name"

to set your account's default identity.
Omit --global to set the identity only to this repository.
```

Git commits must be signed with a name and email, this error states that it cannot find a name or email configured to sign a commit.
* Set your name and email. Do as it said, i.e:

```bash
$ git config --global user.email "slug@ucsc.edu"
$ git config --global user.name "Sammy Slug"
```

* You can also configure the identity of a repository separately:

```bash
$ git config --global user.email mine@personal.com
$ git config --global user.name sluggo_wuggo

# Within a repository for a class
$ git config --local user.email slug@ucsc.edu
$ git config --local user.name “Sammy Slug”
```

{: .note}
In most terminal operations, quotation marks are optional with single-tokens, but is necessary when tokens are space separated like a `"FirstName Last Name"`

## `make`

### "Where is my executable!"

`make` stops when the compiler finds a compilation error, so the final executable cannot be made. Fix all compilation errors and try once more.

{: .note }
Compilation *warnings* are usually excused by the compiler and the executable might be made. As good practice, the goal is the most quiet compilation with no warnings either, so fix warnings as well.
