---
title: "Bash & Command Line"
layout: default
nav_order: 3
has_toc: true
---

# `bash`
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Intro

> ["Why learn `bash` and UNIX?"](https://en.wikipedia.org/wiki/Unix_philosophy)

### Further resources
* Explains each command and options: [explainshell.com](https://explainshell.com/)
* A cheat sheet of many `bash` commands and constructs: [devhints.io/bash](https://devhints.io/bash)

At the top of any script file, it's generally good practice to specify which shell you are using:
```bash
#!/bin/bash
```

---

## Where am I?

Once you have opened a terminal, you will most likely end up somewhere like:
```bash
slug@13s:~/A/B/C$
```

The `~` (tilde) indicates you are in your home directory.

If you were in a folder called C within B within A within the home directory:

```bash
slug@13s:~/A/B/C$
```

| Location     |              |
| ------------ | ------------ |
| `.`          | Refers to the current directory, “here” |
| `..`         | Parent directory, “up” |
| `~`          | Home directory |
| `/dev/null`  | Like a trash can, content written into it disappears | 


**Pro tip**: you can use TAB to autocomplete the name of a file/directory once you have entered enough information for it to autocomplete. If multiple files have the same prefix, TAB can at least cover the prefix.

See the `cd` command for specific information on moving around.

---

## Files and Redirection

File redirection is exactly what it sounds like: putting or taking content from a file. Input into a file is called **`stdin`** for “standard input” (including what you type into the terminal, think of Python’s `input()` function) and output from a file is called **`stdout`** for “standard output” (including what you see appear on the terminal, think of Python’s `print()` function). There is one more path for content to go through: **`stderr`** for “standard error” which is self-explanatory.

UNIX assigns **file descriptors**, which are integers, to files and processes for referencing. All UNIX processes will include `stdin`, `stdout`, and `stderr`. They are assigned as followed:

| Stream        | File descriptor |
| ------------- | :-------------: |
| `stdin`       | 0 |
| `stdout`      | 1 |
| `stderr`      | 2 |

These operators are used to redirect the file streams into other processes.

|           |              |
| --------- | ------------ |
| `<`       | Redirect input (destination < source) |
| `>`       | Redirect output (source > destination), can manually specify to redirect `stdout` with 1> |
| `2>`      | Redirect `stderr` (stderr 2> another process or file) |
| `&>`      | Redirect both `stdout` and `stderr` (stdout/err &> another) |

For example, sending the content of text file a.txt into the sort command:
```bash
sort < a.txt
```

And then storing the sorted lines into b.txt:
```bash
sort < a.txt > b.txt
```

---

## Pipelining

Pipelining allows for connecting the `stdout` of one process into the `stdin` of another without having to store an intermediate file/process. An analogy for this factory’s conveyer belt where each station (process) receives a partially built item (the `stdout` of the previous process going into its `stdin`) and adding onto it before passing it along (repeat).

The syntax for pipelining is read left-to-right, where each pipe is denoted with a vertical bar `|` and where the first item is the first source of input and the last item is the last destination. If a file destination is not specified, the output is usually printed onto the terminal.
```bash
input | first | second | third | output
```
For example, this pipeline will:
1. List out the contents of the current directory 
2. Sort out these contents alphabetically
3. List out the first three items from the sort list of files
```bash
ls | sort | head -n 3
```

An alternative syntax to the previous sorted `a.txt` to `b.txt` example would be:
```bash
cat a.txt | sort > b.txt
```
{. .note} Notice the last part required `a >` to write into `b.txt`. 

Since this was a one step process, it may feel redundant.

Here’s a more realistic example: sorting alphabetically and counting word frequencies, and storing into `b.txt`:

```bash
cat a.txt | sort | uniq -c > b.txt
```


---

## Commands

### Command-Line

### Utility commands

---

## Arithmetic

---

## Logic and Testing


