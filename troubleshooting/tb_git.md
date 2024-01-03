---
title: "Troubleshooting: Git"
parent: Troubleshooting
layout: default
nav_order: 2
has_toc: true
last_modified_date: 2024-01-02 at 3:28 PM
---

# Troubleshooting `git`
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Author identity unknown

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

{: .note}
Notice how this message includes a solution *within* its text, just like many Git errors.

Set your name and email like it said:

```bash
$ git config --global user.email "slug@ucsc.edu"
$ git config --global user.name "Sammy Slug"
```

You can also configure the identity of a repository separately:

```bash
$ git config --global user.email mine@personal.com
$ git config --global user.name sluggo_wuggo

# Within a repository
$ git config --local user.email slug@ucsc.edu
$ git config --local user.name “Sammy Slug”
```

{: .note}
In most terminal operations, quotation marks are optional with single-tokens, but is necessary when tokens are space separated like a `"FirstName Last Name"`

## fatal: Need to specify how to reconcile divergent branches

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

The error is saying there are divergent branches, or inconsistent branch history, and Git needs to know how to deal with them upon a pull. The easiest method is to merge the two branches into one, so enter:
```bash
git config pull.rebase false
git pull # try the pull again
```

## fatal: loose object [ ... ] is corrupt

Upon any Git command, you might get some error like:
```
error: object file .git/objects/... is empty
error: object file .git/objects/... is empty
fatal: loose object ... (stored in .git/objects/...) is corrupt
```

This usually happens with improper shutdowns of your virtual machine.

Navigate to the root of your repository (where `.git` is located)and do:[^1]

```bash
find .git/objects/ -size 0 -exec rm -f {} \;
git fetch origin
```

## fatal: update_ref failed for ref 'ORIG_HEAD'

Upon every push/pull from a remote called `origin`, a file called `ORIGIN_HEAD` is updated. If this file is corrupted, you may get a message similiar to:
```
fatal: update_ref failed for ref 'ORIG_HEAD': ... reference broken
```

To resolve this, you can remove the faulty file and then try your `git push` or `git pull` again.[^2] The file `ORIGIN_HEAD` will be remade.
```bash
# cd to root of your repo
rm .git/ORIGIN_HEAD
```

---

[^1]: loose object fix from [Stack Overflow](https://stackoverflow.com/a/40098509)

[^2]: `ORIGIN_head` fix from [Stack Overflow](https://stackoverflow.com/a/14159830)
