---
title: "Troubleshooting: Make"
parent: Troubleshooting
layout: default
nav_order: 3
has_toc: true
last_modified_date: 2024-01-02 at 3:30 PM
---

# Troubleshooting `make`
{: .no_toc }

{: .tip}
You can find an explanation of many common error messages produced by `make` and their solutions at the [offiical documentation](https://www.gnu.org/software/make/manual/html_node/Error-Messages.html).

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## "Where is my executable!"

`make` stops when the compiler finds a compilation error, so the final executable cannot be made. Fix all compilation errors and try again.

{: .important}
Compilation *warnings* are usually excused by the compiler and the executable might be made. As good practice, the goal is the most quiet compilation with no warnings either. Try fixing warnings when possible.

---

