# Linux File & Directory Management

## Overview

Linux organizes everything in a tree structure starting at the root directory:

    /

Think of it like a filing cabinet:
- `/` = the cabinet
- directories = folders
- files = documents

Understanding how to create, move, copy, and delete files is foundational for Linux system management.

---

## Paths

### Absolute Path
Starts from the root (`/`) and gives the full location:

    /home/lee/file.txt

### Relative Path
Starts from your current location:

    documents/file.txt

---

## Current Location

### pwd (Print Working Directory)

Command structure:

    pwd

Example:

    pwd

Output:

    /home/lee

---

## Listing Files

### ls (list)

Command structure:

    ls [options] [path]

Common options:
- `-a` → show hidden files
- `-l` → detailed listing
- `-h` → human-readable sizes

Example:

    ls -lah

---

## Navigation

### cd (change directory)

Command structure:

    cd [path]

Examples:

    cd /home/lee        # absolute path
    cd documents        # relative path
    cd ..               # move up one directory
    cd                  # go to home directory

---

## Creating Files and Directories

### Create a file

    touch file.txt

### Create a directory

    mkdir folder

---

## Copying Files and Directories

### cp (copy)

Command structure:

    cp source destination

Examples:

    cp file1.txt file2.txt
    cp file.txt folder/

### Copy directories

    cp -r folder1 folder2

Note: `-r` means recursive (copy everything inside)

---

## Moving and Renaming

### mv (move)

Command structure:

    mv source destination

Examples:

    mv file.txt folder/
    mv old.txt new.txt   # rename file

---

## Deleting Files and Directories

### Remove a file

    rm file.txt

### Remove a directory

    rm -r folder

Warning: There is no recycle bin. Deleted files are gone.

---

## Hands-On Labs

### Lab 1: Create and Navigate

Start from your home directory:

    cd ~
    mkdir lab1
    cd lab1

Create files and directories:

    touch file1.txt
    mkdir dir1

List contents:

    ls -lah

Move into directory:

    cd dir1
    pwd

Move back:

    cd ..

---

### Lab 2: Copy and Move

Start from home directory:

    cd ~
    mkdir lab2
    cd lab2

Create files and directories:

    touch fileA.txt
    mkdir dirA

Copy file:

    cp fileA.txt dirA/

Verify:

    ls dirA

Move file:

    mv fileA.txt dirA/

Rename file:

    mv dirA/fileA.txt dirA/fileB.txt

Verify:

    ls dirA

---

### Lab 3: Delete Files and Directories

Start from home directory:

    cd ~
    mkdir lab3
    cd lab3

Create files and directories:

    touch test1.txt
    mkdir testdir
    touch testdir/file1.txt

Delete file:

    rm test1.txt

Delete directory:

    rm -r testdir

Verify:

    ls

---

## Cleanup

Return to home directory:

    cd ~

Remove lab directories:

    rm -r lab1
    rm -r lab2
    rm -r lab3

Verify cleanup:

    ls

---

## Recap Checklist

- I can check my location using `pwd`
- I can list files using `ls -lah`
- I can navigate using `cd`, `..`, and `~`
- I can create files using `touch`
- I can create directories using `mkdir`
- I can copy files using `cp`
- I can move or rename files using `mv`
- I can delete files using `rm`
- I understand `-r` for directories

---

## Flashcards

What is the root directory in Linux?;/;files directories
What is an absolute path?;A full path starting from the root directory;files directories
What is a relative path?;A path relative to the current directory;files directories
What command shows your current directory?;pwd;files directories
What command lists files and directories?;ls;files directories
What does ls -a do?;Shows hidden files;files directories
What does ls -l do?;Shows detailed file information;files directories
What command changes directories?;cd;files directories
What does cd .. do?;Moves up one directory level;files directories
What command creates a file?;touch;files directories
What command creates a directory?;mkdir;files directories
What command copies files?;cp;files directories
What does cp -r do?;Copies directories recursively;files directories
What command moves or renames files?;mv;files directories
What command deletes a file?;rm;files directories
What does rm -r do?;Deletes directories and their contents;files directories

---

**MOC:** [[00 - ChatGPT TEACH ME MOC]]