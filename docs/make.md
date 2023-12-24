---
title: Make
layout: default
nav_order: 5
has_toc: true
---

# `make`
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Intro

`make` is a utility to automate the compilation process of your programs. Instead of having to manually compile your programs, `make` can handle this and give you a simple interface for handling compilation.

`make` is not exclusive to C, they can be used in any situation where you want to keep some `bash` code to reuse.

### Further resources
* Official `make` [documentation](https://www.gnu.org/software/make/manual/make.html)
* Helpful `Makefile` [tutorial](https://makefiletutorial.com/)

### Prereqs

Ensure `make` is installed:

```bash
$ sudo apt-get install make
```

If you are having issues with installing, consult the [troubleshooting page][TS: Installation].

---

## TL;DR

You can (almost) use this out of the box after you change the names. Below is a `Makefile` with verbose comments; see also the [Examples section](#examples) for some example Makefiles (with cool auto-variables!).

```make
# Comments with a # outside of a target body

# Variables
SHELL  := /bin/sh
CC      = gcc
CFLAGS  = -Wall
LFLAGS  = -lm

# Grabbing all the file names of type .c
SOURCES = $(wildcard *.c)

# Replacing all the file names to be of type .o
# NOTE: this is NOT a variable of the files itself, just their names
OBJECTS = $(subst .c,.o,$(SOURCES))

# This is used to specify that PHONY targets are special targets and are not associated with any particular files
.PHONY = all utility1 utility2 ...

# When make is called with just "make", it defaults to "make all"
# It's good practice to specify the all target for this reason
all: target t2 ...

# Targets are called in terminal by running "make target"
# The -o flag names the output to the token after it
target: dependency1 dep2 ...
	$(CC) -o target prog.o
# On the terminal the above line is expanded by make into:
# $ gcc -o prog prog.o

# Example target for a program called foo made with foo.c, and bar.c
# foo.o and bar.o are made with the %.o: %.c target
foo: foo.o bar.o
	$(CC) $(CFLAGS) foo.o bar.o -o foo $(LFLAGS)

# Dependencies can lead to files, or other targets that are also called by make
dependency1: dep3 ...
	bash command

# HELPFUL! Any .o target will be matched to this, compiles the corresponding .c into its .o, use this! CFLAGS is a variable for any additional flags for compilation. May not be needed for this assignment but definitely in future courses.
%.o: %.c
	$(CC) $(CFLAGS) -c $<

# Common to have a clean target to remove anything created by your programs
clean:
	rm -rf *.o foo
```

---

## Purpose

Why use `make`? You don’t necessarily need to, you can manually compile your programs like so:

{: .note}
`$` is usually used to indicate something is being run on a terminal, with each `$` indicating a new command being typed and entered.

```bash
$ gcc -c prog.c
$ gcc -o prog prog.o
$ ./prog
```


This can quickly get annoying, however, when your program will start depending on code across several files like so:

```bash
$ gcc -c fileA.c
$ gcc -c fileB.c
$ gcc -c fileC.c
$ gcc -c prog.c
$ gcc fileA.o fileB.o fileC.o prog.o -o prog
$ ./prog
```

In this example, the main program was coded in `prog.c`, but required code that was in `fileA.c`, `fileB.c`, `fileC.c`. All four files had to be compiled, then assembled together into an executable. One typo and this would fail.

Defining the build process once for these files in a Makefile gives greater reusability and convenience, in addition to helping with complex compilations like the previous example. By providing a Makefile, you are specifying the exact compilation your program needs, and anyone else (aka, the graders!) can compile your program the same exact way you did.

---

## Terms

### Rule
A `make` **rule** has a target, potential dependencies/prerequisites for the target, and the recipe (which is a bash command that is expanded by make). Read the official documentation for a more detailed description.

The typical syntax is:
```make
target: dependencies
	recipe
```

{: .important}
Makefiles must be indented with a TAB, this is usually done for you automatically in Vim or any other IDE that can detect the file type.


#### Target
A **target** is the name of the result, usually a file name (although not always, this will be discussed in `.PHONY`). These are invoked in the terminal by:

```bash
$ make target_name
```

{: .important}
If anything goes wrong during the process of making the target, make will quit and throw the error back at you (such as a compilation error in your code), which will result in the executable not being created.

#### Dependencies
A typical C file to program process is: take your code (`.c`), make an object file from it (`.o`), and then assemble into an executable (`.exe`, but this ending is not shown on UNIX). The `.o` depends on its corresponding `.c`, and the executable depends on the `.o` (or multiple).

{: .note}
> Most compilers allow you to skip the object file creation stage (see below), but it’s beneficial to separate this stage in cases where you are managing several dependencies towards an executable. 
> ```bash
> $ gcc file1.c file2.c file3.c -o output
> ```
> When you separate the object file creation, you will see compilation warnings and errors specific to a file much more clearly than when you use the shortcut. It is highly encouraged to have a target that compiles object files before assembling them together into an executable.

{: .important}
The linking of libraries is done during the object to executable phase (technically, after assembling). For example, see how the math library `-lm` is linked in the [short example.](#single-program)

#### Recipe
A recipe is simply a bash command that tell make how to build the target. This is where you put in the same commands that you were doing manually.

### `.PHONY`
If your target is not meant to be a resulting file or program, it is good practice to tell `make` this. Common ones include `clean`, `all`, `format` which are usually utility targets.

---

## Variables

Variables can be referenced just like a `bash` script. These are later expanded in the recipes (and later in the terminal). Common ones include `SHELL` (link to which shell to use), `CC` (C Compiler), `CFLAGS` (compiler flags), `LFLAGS` (library flags), EXEC` (the name of all programs being created) , `SRC` (source files, i.e C files), and `OBJ` (object files). You can use your own variables to substitute repeated segments in your Makefile and make them simpler.

```make
SHELL  := /bin/sh
CC      = gcc
CFLAGS  = -Wall

foo: foo.o
	$(CC) $(CFLAGS) -o foo foo.o
```

{: .note}
`:=` is used as "immediate" assignment, as in `make` will immediately assign `SHELL` to the system's symbolic link to its default shell (Ubuntu has `/bin/sh` point to `/bin/bash`). `=` means "lazy" assignment, as in the variable's value will not be expanded until it is later used. Read more about assignment operators at the [official documentation.](https://www.gnu.org/software/make/manual/html_node/Setting.html)

{: .note}
The variables don't have to be lined up like they are in this example, but it's plesant to look at.

When `make foo` is called, the recipe is expanded in the terminal:
```bash
$ make foo
gcc -Wall -o foo foo.o
```

---

## A simple start

Here is a quick example of wanting to make a program called bar, which also needs a link to the math library, and at the end will be instructions on how to run it:
```make
CC     = gcc
CFLAGS = -Wall -Wextra -Wpedantic

.PHONY = all clean

# Default for this directory when make is run
all: bar

# Object -> Executable
# -lm links to the math library!
bar: bar.o
	$(CC) $(CFLAGS) -o bar bar.o -lm

# Source -> Object
bar.o: bar.c
	$(CC) $(CFLAGS) -c bar.c

# No executables (like bar) or objects!
clean:
	rm -f *.o bar
```

This should be stored under the file name of `Makefile` (no extension) in the same directory as `bar.c`.
```bash
$ ls
bar.c     Makefile
$ make bar # same result with make all or make
gcc -c bar.c
gcc -o bar bar.o -lm
$ ./bar
Hello World!
```

What if I changed the code in my bar.c? I will have to recompile the program! But to be sure I am not compiling with any older object files, I will use my cleaning utility target.
```bash
$ make clean
rm -f *.o bar
# makes changes to bar.c
$ make bar
gcc -c bar.c
gcc -o bar bar.o -lm
$ ./bar
World Hello!
```

{: .important}
“I changed my code, why does it still have problems!” You must recompile after each code change to see the resultant change take place.

I personally chain these commands together when debugging so it recompiles every time I am rechecking the executable behavior:
```bash
$ make clean && make bar && ./bar
```

---

## Examples

The following are example Makefiles that also utilize [Auto-variables](#auto-variables) to mitigate typo errors and be re-usable! While you don’t need to know an exceptional amount of Makefile information, it helps in conceptualizing the compilation process.

### Single program

{: .note}
This example Makefile also links the math library in the executable. Notice how the library flags come after the rest of the compile command.

{: .important}
Only one of the source files can contain a `main()`, as an executable can be built from multiple object files, but only one of those object files can contain the execution starting point!

```make
CC     = gcc
CFLAGS = -Wall -Wextra -Wpedantic
LFLAGS = -lm

# Get the name of all source files and their corresponding object files
SRC = $(wildcard *.c)
OBJ = $(subst .c,.o,$(SRC))

EXEC = foo

.PHONY = all clean

all: $(EXEC)

# Compile all object files and link libraries to make an executable
$(EXEC): $(OBJ)
	$(CC) $(CFLAGS) $^ -o $@ $(LFLAGS)

# Compile any .c into its corresponding .o
%.o: %.c
	$(CC) $(CFLAGS) -c $<

clean:
	rm -rf *.o $(EXEC)
```

### Multiple programs

The last Makefile assumed the creation of one executable that takes in all objects. What if you have multiple programs to make, each with a different set of dependencies. Use variables to make this easier to manage. Note that I still keep the `all` target (`make`’s default) to make all the executables.

```make
CC     = gcc
CFLAGS = -Wall -Wextra -Wpedantic

# Not helpful in this case since the two programs use separate files :(
# SRC = $(wildcard *.c)
# OBJ = $(subst .c,.o,$(SRC))

FOO_OBJ = foo.o fileA.o fileB.o
BAR_OBJ = bar.o fileC.o fileD.o

EXEC = foo bar

.PHONY = all clean

all: $(EXEC)

# Specifying exactly what foo needs
foo: $(FOO_OBJ)
	$(CC) $(CFLAGS) $^ -o $@

bar: $(BAR_OBJ)
	$(CC) $(CFLAGS) $^ -o $@

%.o: %.c
	$(CC) $(CFLAGS) -c $<

clean:
	rm -rf *.o $(EXEC)
```

What? Those two programs have the exact same recipes? Not so, when the variables are expanded onto terminal, those two targets are executed differently:
```
$ make
# "make" = "make all" -> all is specified to run foo and bar
# First need to generate those object file dependencies
gcc -Wall -Wextra -Wpedantic -c foo.c
gcc -Wall -Wextra -Wpedantic -c fileA.c
gcc -Wall -Wextra -Wpedantic -c fileB.c

# Now assemble the executable
gcc -Wall -Wextra -Wpedantic foo.o fileA.o fileB.o -o foo

# Done with foo, repeat with bar
gcc -Wall -Wextra -Wpedantic -c bar.c
gcc -Wall -Wextra -Wpedantic -c fileC.c
gcc -Wall -Wextra -Wpedantic -c fileD.c
gcc -Wall -Wextra -Wpedantic bar.o fileC.o fileD.o -o bar

# After I'm done with the executables, clean up
$ make clean
rm -rf *.o foo bar
```

---

## Auto-variables

Auto variables are helpful shortcuts to refer to parts of the Makefile, similar to wildcards in bash scripts. There are tons of them, read about them in the [official documentation!](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html)

My personal favorite usage of auto-variables comes in the form:
```make
%.o: %.c
	$(CC) $(CFLAGS) -c $<
```

This compiles any .c in my directory into its object file, with the specified compiler and compiler flags (unless specified, don’t worry about this, it is optional), and names it after the first dependency (which is the .c in question). Use this everywhere!

---

## Conclusion

`make` and `Makefile`s are somewhat of an art, and if you can generalize these well enough, you will only have to make minor customizations from your template for the rest of your journey in programming in C.

---

[TS: Installation]: troubleshooting.html
