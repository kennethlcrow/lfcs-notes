# Hard Links and Soft Links (Symbolic Links)

## Overview

In Linux, files are not just simple names pointing to data. Each file is associated with an **inode**, which stores metadata and points to the actual data on disk.

Understanding how **hard links** and **soft links (symbolic links)** work is essential for managing files efficiently.

---

## Key Concepts

### Inode
An inode stores:
- File metadata (permissions, owner, size)
- Pointer to the actual data blocks on disk

### File Name vs Data
- The filename is just a reference
- The inode connects the filename to the data

---

## Hard Links

### Definition
A **hard link** is another filename that points to the **same inode** as an existing file.

### Command Structure

    ln source_file new_link_name

### Example

    ln file.txt hardlink.txt

### What Happens
- Both filenames point to the same inode
- No duplicate data is created
- Both names are equal (no “original” file)

### Verification

    ls -li

Look for matching inode numbers.

### Behavior
- Deleting one name does NOT delete the data
- Data is removed only when all hard links are deleted

### Limitations
- Cannot link directories
- Cannot cross filesystems

---

## Soft Links (Symbolic Links)

### Definition
A **soft link** points to a **file path**, not an inode.

### Command Structure

    ln -s target link_name

### Example

    ln -s file.txt softlink.txt

### What Happens
- The link stores the path to the target file
- Acts like a shortcut

### Verification

    ls -l

Soft links appear with an arrow:

    softlink.txt -> file.txt

### Behavior
- If the target is deleted, the link breaks
- Can link directories
- Can cross filesystems

---

## Hard vs Soft Links

| Feature | Hard Link | Soft Link |
|--------|----------|----------|
| Points to | Inode | File path |
| Data duplication | No | No |
| Survives deletion of original | Yes | No |
| Cross filesystem | No | Yes |
| Link directories | No | Yes |
| Appearance | Normal file | Link with arrow |

---

## Hands-On Labs

### LAB 1 — Create and Inspect Hard Links

    mkdir lab1
    cd lab1

    touch file.txt
    echo "Hello world" > file.txt

    ln file.txt hardlink.txt

    ls -li

    rm file.txt
    cat hardlink.txt

---

### LAB 2 — Create and Break a Soft Link

    mkdir lab2
    cd lab2

    touch file.txt
    echo "Soft link test" > file.txt

    ln -s file.txt softlink.txt

    ls -l
    cat softlink.txt

    rm file.txt
    cat softlink.txt

---

### LAB 3 — Compare Hard and Soft Links

    mkdir lab3
    cd lab3

    touch original.txt
    echo "Compare links" > original.txt

    ln original.txt hard.txt
    ln -s original.txt soft.txt

    ls -li

    rm original.txt

    cat hard.txt
    cat soft.txt

---

## Cleanup

    cd ..
    rm -r lab1
    rm -r lab2
    rm -r lab3

---

## Recap Checklist

- Understand what an inode is
- Know that hard links point to the same inode
- Know that deleting one hard link does not delete data
- Understand that soft links point to a path
- Know that soft links break if the target is deleted
- Know how to create links using ln and ln -s
- Know how to identify links using ls -l and ls -li

---

## Flashcards

What does a hard link point to?;The same inode as the original file;hard-links soft-links file-system
What command creates a hard link?;ln source_file new_link;hard-links soft-links file-system
What happens when you delete one hard link?;The data remains as long as another link exists;hard-links soft-links file-system
What must match for hard links to work?;They must be on the same filesystem;hard-links soft-links file-system
Can hard links be created for directories?;No;hard-links soft-links file-system
What does a soft link point to?;A file path;hard-links soft-links file-system
What command creates a soft link?;ln -s target link_name;hard-links soft-links file-system
What happens to a soft link if the target is deleted?;It becomes broken;hard-links soft-links file-system
How do you identify a soft link?;It shows an arrow in ls -l output;hard-links soft-links file-system
What command shows inode numbers?;ls -li;hard-links soft-links file-system
Do hard links duplicate data?;No;hard-links soft-links file-system
Can soft links cross filesystems?;Yes;hard-links soft-links file-system
Which link type behaves like a shortcut?;Soft link;hard-links soft-links file-system
When is data deleted from a hard link?;When all links to the inode are removed;hard-links soft-links file-system
Which link type is more fragile?;Soft link;hard-links soft-links file-system

---

**MOC:** [[00 - ChatGPT TEACH ME MOC]]