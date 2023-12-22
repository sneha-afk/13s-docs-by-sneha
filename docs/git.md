---
title: Git
layout: default
nav_order: 4
has_toc: true
---

# `git`
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Intro

> Git is one of the most important tools you will learn, other than knowing `:wq` (thank me later).

Git is a version control system, similar to labeling your essay drafts “essay”, “essay_draft2”, “essay_feedback”, “essay_final”, “ESSAY_FINAL_FINAL.” It is imperative to know Git, and know it well. Situations like being stuck in Baskin’s E2 building during a horrible winter storm when the power, internet, and cell service go down in the middle of working on a CSE13S assignment is when you want to know Git (true story).

I have used Git to back up and submit my school work, work on open-source projects, and collaborate with others seamlessly. You *must* learn the basics of Git.


### Prereqs

Ensure `git` is installed:

```bash
$ sudo apt-get install git
```

If you are having issues with installing, consult the [troubleshooting page][TS: Installation].


## TL;DR

For most of your assignments and projects that you do individually, all you have to remember is:

```bash
git add <file1> <file2> …
git commit -m "committing!"
git push [origin main]
```

Add, commit, push. Add, commit, push.

Previewing [Commands](#commands), the “`origin main`” (Note: the default branch may be also be called `master`) does not have to be specified if you’re working with one remote and one branch (more about these terms later), but it’s the safest way to know exactly where you are pushing, and I recommend those two arguments each time to prevent errors.

## Terms

### Repository

### Commit

### Hash

### Remote

### Branch

### And there's more.

## Commands

### `git clone`

### `git add`

### `git restore`

### `git commit`

### `git push`

### `git pull`

### `git log`

### `git remote`

### `git status`

### `git config`

### `git checkout`

### Extra commands

## Best practices

### Setting up SSH keys

### `.gitignore`


---

[TS: Installation]: troubleshooting.md/#installation

