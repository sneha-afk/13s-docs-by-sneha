---
title: "Troubleshooting: General"
parent: Troubleshooting
layout: default
nav_order: 1
has_toc: true
---

# Troubleshooting Ubuntu and other general issues
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## "`username` is not in the sudoers file"

Many operations in UNIX require super user (`sudo`) permissions. Without these permissions, you might see:
```
<username> is not in the sudoers file. This incident will be reported
```

Run the following commands:[^1]
```bash
$ su
$ password: <password of your VM>
$ usermod -aG sudo <username>
```

Restart your VM, and try the command again.

## E: Unable/Failed to fetch some archives

`apt` is a package manager and relies on online archives that point to the source of these packages. You may run into this error when attempting to install via `sudo apt installl` or when upgrading packages with `sudo apt upgrade`:
```bash
Failed to fetch ...
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```

You should update the package references via:
```bash
sudo apt-get update
```

{: .note}
It's best to install using `sudo apt-get install <name>` as `apt-get` achieves a multitude of actions.[^2]

---

[^1]: sudoers file fix from [Stack Overflow](https://stackoverflow.com/a/47810801)

[^2]: Explanation of `apt-get` from [Ask Ubuntu](https://askubuntu.com/a/540943)

