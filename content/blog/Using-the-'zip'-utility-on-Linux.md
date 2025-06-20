---
title: Using the `zip` utility on Linux
date: 2025-06-18T11:03:28.000+00:00
draft: false
tags:
- linux
- dev-ops
- terminal
---

The `zip` utility is a widely used command-line tool for compressing files and directories into `.zip` archives. It is simple, fast, and available on most Linux systems. This guide explains how to install `zip` from standard repositories on various Linux distributions and how to use it with practical examples.

---

## 📦 Installing `zip` on Popular Linux Distributions

### Ubuntu / Debian

Update your package lists and install:

```bash
sudo apt update
sudo apt install zip
```

### Fedora

```bash
sudo dnf install zip
```

### CentOS / RHEL 8+

```bash
sudo dnf install zip
```

For CentOS 7 or older versions of RHEL:

```bash
sudo yum install zip
```

### Arch Linux / Manjaro

```bash
sudo pacman -S zip
```

---

## 🛠 Creating Zip Archives

### Zip One or More Files

```bash
zip archive-name.zip file1.txt file2.jpg
```

This creates a file called `archive-name.zip` containing `file1.txt` and `file2.jpg`.

### Zip a Directory Recursively

```bash
zip -r archive-name.zip directory-name/
```

The `-r` flag tells `zip` to include all subdirectories and files.

### Add Files to an Existing Archive

```bash
zip existing.zip newfile.txt
```

This adds `newfile.txt` to `existing.zip`.

---

## 🔐 Useful Options

| Option | Description |
|--------|-------------|
| `-r`   | Recursively include directories |
| `-e`   | Encrypt the archive with a password |
| `-9`   | Use maximum compression |
| `-q`   | Quiet mode (suppress output) |

### Example: Create a password-protected, high-compression zip

```bash
zip -r -e -9 secure-archive.zip myfolder/
```

---

## 📂 Extracting Zip Files

To extract `.zip` files, use the `unzip` utility. It may already be installed; if not, install it using the same package manager.

### Ubuntu / Debian

```bash
sudo apt install unzip
```

### Fedora

```bash
sudo dnf install unzip
```

### Arch Linux / Manjaro

```bash
sudo pacman -S unzip
```

### Extract a Zip File

```bash
unzip archive-name.zip
```

### Extract to a Specific Directory

```bash
unzip archive-name.zip -d target-directory/
```

---

## ✅ Summary

The `zip` command is a core tool for compressing files on Linux. With broad support across distributions and simple syntax, it’s perfect for:

- Archiving and sharing files
- Creating compressed backups
- Automating packaging in scripts

Once installed, you can start zipping files with just a few keystrokes. It’s a must-have utility in any Linux user’s toolbox.
