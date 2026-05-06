# Creating, Deleting, Copying, and Moving Files and Directories

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

## Source Context

This note is based on LFCS Lesson 13: essential file and directory management commands in Linux.

## Big Picture

Linux organizes files and directories in one large tree.

The top of the tree is the root directory:

    /

From `/`, Linux branches into directories such as:

    /home
    /etc
    /var
    /tmp

A directory is like a folder. A file is an item stored inside a directory.

Before creating, copying, moving, or deleting anything, always know where you are in the file system.

## Key Concepts and Definitions

### File

A file stores data, such as text, configuration, logs, or scripts.

Example:

    notes.txt

### Directory

A directory stores files and other directories.

Example:

    documents

### Root Directory

The root directory is the top of the Linux file system tree.

It is written as:

    /

### Current Working Directory

Your current working directory is the directory your terminal is currently operating inside.

Use:

    pwd

to print your current working directory.

### Absolute Path

An absolute path starts from the root directory `/`.

Example:

    /home/student/documents

This path works no matter where you currently are.

### Relative Path

A relative path starts from your current working directory.

Example:

    documents

If you are currently in `/home/student`, then `documents` means:

    /home/student/documents

## Command Structure Breakdown

### pwd

Structure:

    pwd

Meaning:

- `pwd` means print working directory
- It shows your current location

Example:

    pwd

### ls

Structure:

    ls [options] [location]

Meaning:

- `ls` lists files and directories
- `[options]` change the output
- `[location]` tells Linux where to list contents

Common examples:

    ls
    ls -la
    ls -lah

Option meanings:

- `-l` shows a long detailed listing
- `-a` shows all files, including hidden files
- `-h` shows human-readable file sizes when used with `-l`

### cd

Structure:

    cd [directory]

Meaning:

- `cd` means change directory
- It moves you into another directory

Examples:

    cd documents
    cd /home/student/documents
    cd ..
    cd

Useful forms:

- `cd documents` moves into a directory named `documents` from your current location
- `cd /home/student/documents` uses an absolute path
- `cd ..` moves up one directory
- `cd` by itself returns to your home directory

### touch

Structure:

    touch [file-name]

Meaning:

- Creates an empty file if it does not exist
- Updates the timestamp if the file already exists

Example:

    touch notes.txt

### mkdir

Structure:

    mkdir [directory-name]

Meaning:

- Creates a directory

Example:

    mkdir documents

### cp

Structure for files:

    cp [source] [destination]

Meaning:

- Copies a file from a source to a destination

Example:

    cp notes.txt notes-backup.txt

Structure for directories:

    cp -r [source-directory] [destination-directory]

Meaning:

- `-r` means recursive
- Recursive copying copies the directory and its contents

Example:

    cp -r project project-backup

### mv

Structure:

    mv [source] [destination]

Meaning:

- Moves a file or directory
- Also renames files or directories

Move example:

    mv notes.txt documents/

Rename example:

    mv draft.txt final.txt

### rm

Structure for files:

    rm [file-name]

Meaning:

- Deletes a file

Example:

    rm notes.txt

Structure for directories:

    rm -r [directory-name]

Meaning:

- Deletes a directory and its contents recursively

Example:

    rm -r old-folder

Important safety note:

Files removed with `rm` usually do not go to a recycle bin. Be careful and check your location with `pwd` and your targets with `ls` before deleting.

## Clean Examples

Create a file:

    touch file1.txt

Create a directory:

    mkdir documents

Copy a file:

    cp file1.txt file1-copy.txt

Copy a directory:

    cp -r documents documents-backup

Move a file into a directory:

    mv file1.txt documents/

Rename a file:

    mv old-name.txt new-name.txt

Delete a file:

    rm file1-copy.txt

Delete a directory and its contents:

    rm -r documents-backup

## Hands-On Lab 1: Create Files and Directories

Start from the parent directory where you want `lab1`, `lab2`, and `lab3` to be created.

Step 1: Create and enter the lab directory.

    mkdir lab1
    cd lab1

Step 2: Check where you are.

    pwd

Step 3: Create two empty files.

    touch file1.txt
    touch file2.txt

Step 4: Create two directories.

    mkdir documents
    mkdir backups

Step 5: List everything in long format.

    ls -la

Step 6: Create a hidden file.

    touch .hiddenfile

Step 7: List all files, including hidden files.

    ls -la

Step 8: Return to the parent directory.

    cd ..

## Hands-On Lab 2: Copy Files and Directories

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

Step 5: List the copied file.

    ls -la copies

Step 6: Create a directory with a file inside it.

    mkdir project
    touch project/readme.txt

Step 7: Copy the entire directory.

    cp -r project project-backup

Step 8: List the copied directory.

    ls -la project-backup

Step 9: Return to the parent directory.

    cd ..

## Hands-On Lab 3: Move, Rename, and Delete Files and Directories

Start from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

Step 1: Create and enter the lab directory.

    mkdir lab3
    cd lab3

Step 2: Create a file to rename.

    touch draft.txt

Step 3: Rename the file.

    mv draft.txt final.txt

Step 4: List the files to confirm the rename.

    ls -la

Step 5: Create a directory.

    mkdir archive

Step 6: Move the file into the directory.

    mv final.txt archive/

Step 7: List the directory contents.

    ls -la archive

Step 8: Create a temporary file.

    touch temporary.txt

Step 9: Delete the temporary file.

    rm temporary.txt

Step 10: Create a temporary directory with a file inside it.

    mkdir old-folder
    touch old-folder/old-file.txt

Step 11: Delete the temporary directory and its contents.

    rm -r old-folder

Step 12: List everything to confirm what remains.

    ls -la

Step 13: Return to the parent directory.

    cd ..

## Cleanup

Assume you are running cleanup from the same parent directory where the lab directories were created.

Step 1: Check your current location.

    pwd

Step 2: If you are inside `lab1`, `lab2`, or `lab3`, return to the parent directory.

    cd ..

Step 3: Remove the first lab directory.

    rm -r lab1

Step 4: Remove the second lab directory.

    rm -r lab2

Step 5: Remove the third lab directory.

    rm -r lab3

Step 6: List the current directory to confirm the lab directories are gone.

    ls -la

## Recap Checklist

- `pwd` shows your current directory.
- `ls -la` lists files, directories, and hidden files in detail.
- `ls -lah` also makes file sizes easier to read.
- `cd` changes directories.
- `cd ..` moves up one directory.
- `cd` alone returns to your home directory.
- Absolute paths start from `/`.
- Relative paths start from your current location.
- `touch file.txt` creates an empty file.
- `mkdir directory-name` creates a directory.
- `cp source destination` copies files.
- `cp -r source-directory destination-directory` copies directories.
- `mv source destination` moves or renames files and directories.
- `rm file.txt` deletes a file.
- `rm -r directory-name` deletes a directory and its contents.
- Always check your location and target before deleting anything.

## Flashcards

What does `pwd` show?;It shows the current working directory.;file-management
What is the root directory in Linux?;The root directory is the top of the file system tree and is written as `/`.;file-management
What is an absolute path?;An absolute path starts from `/` and gives the full location from the root directory.;file-management
What is a relative path?;A relative path starts from your current working directory.;file-management
What does `ls -la` do?;It lists files and directories in long format and includes hidden files.;file-management
What does the `-h` option do with `ls -l`?;It makes file sizes human-readable.;file-management
What does `cd ..` do?;It moves up one directory level.;file-management
What does `cd` by itself do?;It returns you to your home directory.;file-management
What does `touch file.txt` do?;It creates an empty file named `file.txt` if it does not already exist.;file-management
What does `mkdir notes` do?;It creates a directory named `notes`.;file-management
What is the basic structure of the `cp` command for files?;`cp source destination` copies a file from the source to the destination.;file-management
Why is `cp -r` used for directories?;The `-r` option copies a directory and its contents recursively.;file-management
What is the basic structure of the `mv` command?;`mv source destination` moves or renames a file or directory.;file-management
How can `mv` rename a file?;Use the old file name as the source and the new file name as the destination.;file-management
What does `rm file.txt` do?;It deletes the file named `file.txt`.;file-management
Why should you be careful with `rm`?;Files deleted with `rm` usually do not go to a recycle bin.;file-management
Why is `rm -r` used for directories?;It removes a directory and its contents recursively.;file-management
What should you check before deleting files or directories?;Check your current location with `pwd` and confirm the target with `ls`.;file-management
