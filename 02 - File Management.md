# File Management

Tags: file-management

## Overview

Linux stores files and directories in a tree structure. The top of the tree is the root directory, written as:

    /

Everything on the system lives somewhere under `/`.

A file is a piece of data, such as a text file or configuration file. A directory is a container that can hold files and other directories.

This lesson covers the basic commands used to create, delete, copy, move, rename, list, and navigate files and directories.

## Key Concepts and Definitions

### Root Directory

The root directory is the top-level directory in Linux.

It is written as:

    /

All files and directories are located somewhere under `/`.

### Current Working Directory

The current working directory is the directory your terminal is currently working inside.

Use `pwd` to show it:

    pwd

### Absolute Path

An absolute path starts from the root directory `/`.

Example:

    /home/student/Documents/file.txt

This path works no matter where you currently are.

### Relative Path

A relative path starts from your current working directory.

Example:

    Documents/file.txt

If you are currently in `/home/student`, then `Documents/file.txt` means:

    /home/student/Documents/file.txt

### Useful Path Symbols

    .    current directory
    ..   parent directory
    ~    your home directory

## Command Structure Breakdown

### Show Current Directory

Command structure:

    pwd

`pwd` prints the full path of your current working directory.

Example:

    pwd

### List Files and Directories

Command structure:

    ls [options] [path]

Parts:

- `ls` lists files and directories.
- `[options]` change how the command behaves.
- `[path]` tells `ls` where to list contents from.

Common options:

    -a   show all files, including hidden files
    -l   use long listing format
    -h   show human-readable file sizes when used with -l

Examples:

    ls
    ls -la
    ls -lah

### Change Directory

Command structure:

    cd [path]

Parts:

- `cd` means change directory.
- `[path]` is the location you want to move into.

Examples:

    cd /home/student
    cd Documents
    cd ..
    cd

`cd ..` moves up one directory.

`cd` by itself returns you to your home directory.

### Create a File

Command structure:

    touch [filename]

Parts:

- `touch` creates an empty file if it does not already exist.
- `[filename]` is the name of the file to create.

Example:

    touch notes.txt

### Create a Directory

Command structure:

    mkdir [directory-name]

Parts:

- `mkdir` means make directory.
- `[directory-name]` is the name of the directory to create.

Example:

    mkdir projects

### Copy a File

Command structure:

    cp [source] [destination]

Parts:

- `cp` means copy.
- `[source]` is the file you want to copy.
- `[destination]` is where the copy should go.

Examples:

    cp notes.txt backup.txt
    cp notes.txt documents/

### Copy a Directory

Command structure:

    cp -r [source-directory] [destination-directory]

Parts:

- `cp` means copy.
- `-r` means recursive.
- Recursive copying includes the directory and its contents.

Example:

    cp -r project project_backup

### Move or Rename a File or Directory

Command structure:

    mv [source] [destination]

Parts:

- `mv` means move.
- `[source]` is the file or directory you want to move or rename.
- `[destination]` is the new location or new name.

Move a file into a directory:

    mv notes.txt documents/

Rename a file:

    mv oldname.txt newname.txt

Rename a directory:

    mv oldfolder newfolder

### Delete a File

Command structure:

    rm [filename]

Parts:

- `rm` means remove.
- `[filename]` is the file to delete.

Example:

    rm notes.txt

Be careful with `rm`. Linux usually does exactly what you ask, and deleted files may not be easy to recover.

### Delete a Directory and Its Contents

Command structure:

    rm -r [directory-name]

Parts:

- `rm` means remove.
- `-r` means recursive.
- `[directory-name]` is the directory to delete.

Example:

    rm -r oldfolder

Use this carefully. Only remove directories you are sure you no longer need.

## Clean Examples

Create a file:

    touch notes.txt

Create a directory:

    mkdir documents

Copy a file:

    cp notes.txt notes_backup.txt

Copy a directory:

    cp -r documents documents_backup

Move a file into a directory:

    mv notes.txt documents/

Rename a file:

    mv notes_backup.txt final_notes.txt

Delete a file:

    rm final_notes.txt

Delete a directory and its contents:

    rm -r documents_backup

## Hands-On Labs

### Lab 1: Create Files and Directories

Start from the parent directory where you want your labs to be created.

Step 1: Create and enter the lab directory.

    mkdir lab1
    cd lab1

Step 2: Check where you are.

    pwd

Step 3: List the current contents.

    ls -la

Step 4: Create two empty files.

    touch file1.txt
    touch file2.txt

Step 5: Create two directories.

    mkdir documents
    mkdir backups

Step 6: List everything again.

    ls -la

Step 7: Move into the `documents` directory.

    cd documents

Step 8: Check your current location.

    pwd

Step 9: Return to the parent directory of `documents`.

    cd ..

Step 10: Confirm you are back inside `lab1`.

    pwd

What you practiced:

- Creating files with `touch`
- Creating directories with `mkdir`
- Checking your location with `pwd`
- Listing contents with `ls -la`
- Moving around with `cd`

### Lab 2: Copy Files and Directories

Start from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

Step 1: Create and enter the lab directory.

    mkdir lab2
    cd lab2

Step 2: Create a file.

    touch original.txt

Step 3: Create a directory for copied files.

    mkdir copies

Step 4: Copy the file into the `copies` directory.

    cp original.txt copies/

Step 5: List the current directory.

    ls -la

Step 6: List the `copies` directory.

    ls -la copies

Step 7: Create a directory with a file inside it.

    mkdir project
    touch project/readme.txt

Step 8: Copy the entire `project` directory.

    cp -r project project_backup

Step 9: List the current directory to confirm both directories exist.

    ls -la

Step 10: List the copied directory.

    ls -la project_backup

What you practiced:

- Copying files with `cp`
- Copying files into directories
- Copying directories with `cp -r`

### Lab 3: Move, Rename, and Delete Files and Directories

Start from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

Step 1: Create and enter the lab directory.

    mkdir lab3
    cd lab3

Step 2: Create a file.

    touch draft.txt

Step 3: Rename the file.

    mv draft.txt final.txt

Step 4: Create a directory.

    mkdir documents

Step 5: Move the file into the directory.

    mv final.txt documents/

Step 6: List the `documents` directory.

    ls -la documents

Step 7: Create another directory.

    mkdir old_folder

Step 8: Rename the directory.

    mv old_folder new_folder

Step 9: List the current directory.

    ls -la

Step 10: Delete the file inside `documents`.

    rm documents/final.txt

Step 11: Delete the empty `documents` directory.

    rmdir documents

Step 12: Delete the `new_folder` directory.

    rmdir new_folder

Step 13: List the current directory to confirm the items are gone.

    ls -la

What you practiced:

- Renaming files with `mv`
- Moving files with `mv`
- Renaming directories with `mv`
- Deleting files with `rm`
- Deleting empty directories with `rmdir`

## Cleanup

Assume you are running cleanup from the same parent directory where `lab1`, `lab2`, and `lab3` were created.

Step 1: Check your current directory.

    pwd

Step 2: If you are inside `lab1`, move back to the parent directory.

    cd ..

Step 3: If you are inside `lab2`, move back to the parent directory.

    cd ..

Step 4: If you are inside `lab3`, move back to the parent directory.

    cd ..

Step 5: Remove `lab1` and everything created inside it.

    rm -r lab1

Step 6: Remove `lab2` and everything created inside it.

    rm -r lab2

Step 7: Remove `lab3`.

    rm -r lab3

Step 8: List the current directory to confirm the lab directories are gone.

    ls -la

No users, groups, or services were created during these labs.

## Recap Checklist

- [ ] I can explain the Linux file system tree.
- [ ] I know that `/` represents the root directory.
- [ ] I can use `pwd` to show my current working directory.
- [ ] I can use `ls -la` to list files, directories, hidden files, and details.
- [ ] I understand the difference between absolute and relative paths.
- [ ] I can use `cd` to move between directories.
- [ ] I know that `cd ..` moves up one directory.
- [ ] I know that `cd` alone returns to my home directory.
- [ ] I can create files with `touch`.
- [ ] I can create directories with `mkdir`.
- [ ] I can copy files with `cp`.
- [ ] I can copy directories with `cp -r`.
- [ ] I can move and rename files or directories with `mv`.
- [ ] I can delete files with `rm`.
- [ ] I can delete directories and their contents with `rm -r`.
- [ ] I understand that delete commands should be used carefully.

## Flashcards

What does the Linux root directory represent?;The top of the Linux file system tree, written as `/`.;file-management
What command shows your current working directory?;`pwd` shows the full path of the directory your terminal is currently in.;file-management
What is an absolute path?;A path that starts from the root directory `/` and works no matter where you currently are.;file-management
What is a relative path?;A path that starts from your current working directory.;file-management
What does `ls` do?;It lists files and directories.;file-management
What does `ls -la` show?;It lists files and directories in long format, including hidden files.;file-management
What does the `-h` option do with `ls -l`?;It shows file sizes in a human-readable format.;file-management
What command changes directories?;`cd [path]` changes to the specified directory.;file-management
What does `cd ..` do?;It moves up to the parent directory.;file-management
What does `cd` by itself do?;It returns you to your home directory.;file-management
What command creates an empty file?;`touch [filename]` creates an empty file if it does not already exist.;file-management
What command creates a directory?;`mkdir [directory-name]` creates a new directory.;file-management
What is the basic structure of the `cp` command?;`cp [source] [destination]` copies a file from the source to the destination.;file-management
Why is `cp -r` used for directories?;The `-r` option copies a directory recursively, including its contents.;file-management
What can the `mv` command do?;It can move files or directories and can also rename them.;file-management
What is the basic structure of the `rm` command?;`rm [filename]` removes a file.;file-management
Why should `rm -r` be used carefully?;It removes a directory and its contents recursively, and deleted data may be difficult to recover.;file-management
What must exist before moving a file into a directory?;The source file and destination directory must both exist.;file-management
