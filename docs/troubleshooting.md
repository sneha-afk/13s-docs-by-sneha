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
{: .no_toc .text-delta }

1. TOC
{:toc}

## General

### "`username` is not in the sudoers file"

Many operations in UNIX require super user (`sudo`) permissions. Without these permissions, you might see:
```
<username> is not in the sudoers file. This incident will be reported
```

Run the following commands:[^2]
```bash
$ su
$ password: <password of your VM>
$ usermod -aG sudo <username>
```

Restart your VM, and try the command again.

### E: Unable/Failed to fetch some archives

`apt` is a package manager and relies on online archives that point to the source of these packages. You may run into this error when attempting to install via `sudo apt installl` or when upgrading packages with `sudo apt upgrade`:
```
Failed to fetch ...
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```

You should update the package references via:
```bash
sudo apt-get update
```

{: .note}
It's best to install using `sudo apt-get install <name>` as `apt-get` achieves a multitude of actions.[^3]

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

# Within a repository
$ git config --local user.email slug@ucsc.edu
$ git config --local user.name “Sammy Slug”
```

{: .note}
In most terminal operations, quotation marks are optional with single-tokens, but is necessary when tokens are space separated like a `"FirstName Last Name"`

### fatal: Need to specify how to reconcile divergent branches

Upon a `git pull`, you may get:
```
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint: 
hint:   git config pull.rebase false  # merge
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint: 
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches
```

{: .note}
Notice how this message includes a solution *within* its text, just like many Git errors.

The error is saying there are divergent branches, or inconsistent branch history, and Git needs to know how to deal with them upon a pull. The easiest method is to merge the two branches into one, so enter:
```bash
git config pull.rebase false
git pull # try the pull again
```

### fatal: loose object [ ... ] is corrupt

Upon any Git command, you might get some error like:
```
error: object file .git/objects/... is empty
error: object file .git/objects/... is empty
fatal: loose object ... (stored in .git/objects/...) is corrupt
```

This usually happens with improper shutdowns of your virtual machine.

Navigate to the root of your repository (where `.git` is located):[^1]

```bash
find .git/objects/ -size 0 -exec rm -f {} \;
git fetch origin
```

---

## `make`

### "Where is my executable!"

`make` stops when the compiler finds a compilation error, so the final executable cannot be made. Fix all compilation errors and try once more.

{: .note }
Compilation *warnings* are usually excused by the compiler and the executable might be made. As good practice, the goal is the most quiet compilation with no warnings either, so fix warnings as well.

---

[^1]: loose object fix from [Stack Overflow](https://stackoverflow.com/a/40098509)

[^2]: sudoers file fix from [Stack Overflow](https://stackoverflow.com/a/47810801)

[^3]: Explanation of `apt-get` from [Stack Overflow](https://askubuntu.com/a/540943)
