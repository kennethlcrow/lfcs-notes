# Creating And Managing Hard Links And Soft Links

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

## Original Prompt

TEACH ME how to create and manage hard links and soft links.

Lesson scope:

- Understanding hard links
- Inodes
- Creating hard links with `ln`
- Data persistence through shared inodes
- Limitations of hard links
- Practical uses for hard links
- Soft links / symbolic links
- Basic inspection commands

## Course Notes Context

Matching LFCS course notes were found in:

- `Essential Commands.md`
- `Filesystems.md`

The notes covered hard links, soft links, inodes, `ln`, `readlink`, `ls -l`, `ls -i`, same-filesystem limits, and broken symbolic links.

## Key Concepts

### What Is A Link?

A link is another way to reach a file.

Think of a file as having two parts:

1. The filename you see, such as `notes.txt`
2. The actual file data stored on disk

Linux tracks file data using an inode.

### Inode

An inode is like an ID card for a file.

It stores information about the file and points to the actual data blocks on disk.

Important inode-related ideas:

- A filename points to an inode.
- The inode points to the file's data.
- Multiple filenames can point to the same inode.
- `ls -i` or `ls -li` can show inode numbers.

### Hard Link

A hard link is another filename that points to the same inode as an existing file.

Example idea:

    notes.txt  -> same inode -> actual file data
    copy.txt   -> same inode -> actual file data

Both names are equal. After a hard link exists, there is no special "original" name.

If you delete one name, the data still exists as long as another hard link still points to the same inode.

### Soft Link

A soft link is also called a symbolic link or symlink.

A soft link points to a path, not directly to the inode.

Example idea:

    shortcut.txt -> points to path -> notes.txt -> inode -> actual file data

This is similar to a Windows shortcut.

If the target path is removed or renamed, the soft link breaks.

## Hard Links vs Soft Links

| Feature | Hard Link | Soft Link |
|---|---|---|
| Points to | Same inode | Path |
| Works if original filename is deleted? | Yes, if another hard link remains | No, usually breaks |
| Can link to directories? | Usually no | Yes |
| Can cross filesystems? | No | Yes |
| Has same inode as target? | Yes | No |
| Common command | `ln file link` | `ln -s target link` |

## Command Structure

### Create A Hard Link

    ln target link_name

Breakdown:

- `ln` creates a link.
- `target` is the existing file.
- `link_name` is the new filename you want to create.

Example:

    ln file.txt hardlink.txt

This creates `hardlink.txt` as another filename for the same inode used by `file.txt`.

### Create A Soft Link

    ln -s target link_name

Breakdown:

- `ln` creates a link.
- `-s` means symbolic link.
- `target` is the file or directory path the symlink points to.
- `link_name` is the shortcut name.

Example:

    ln -s file.txt shortcut.txt

This creates `shortcut.txt` as a symbolic link pointing to the path `file.txt`.

### Inspect Links

Show inode numbers and link counts:

    ls -li

Show file types and symlink targets:

    ls -l

Show what path a symlink points to:

    readlink shortcut.txt

## Practical Uses

Hard links are useful when:

- You want multiple filenames for the same data.
- You want to avoid storing duplicate copies of the same file.
- You want data to remain available even if one filename is removed.

Soft links are useful when:

- You want a shortcut to a file.
- You want a shortcut to a directory.
- You need to point across filesystems.
- You want a flexible path-based reference.

## Common Pitfalls

- A hard link is not a separate copy of the file data.
- Changing a file through one hard link changes the same data seen through all hard links.
- Deleting one hard link does not remove the data if another hard link remains.
- A soft link can break if the target path is deleted or renamed.
- Hard links usually cannot be created for directories.
- Hard links cannot cross filesystem boundaries.

## Hands-On Labs

### Lab 1: Create And Inspect A Hard Link

Start from an empty parent directory where you want the labs created.

    mkdir lab1
    cd lab1
    echo "LFCS hard link practice" > original.txt
    ln original.txt hardlink.txt
    ls -li
    cat original.txt
    cat hardlink.txt
    echo "Added through hardlink" >> hardlink.txt
    cat original.txt
    cat hardlink.txt
    rm original.txt
    ls -li
    cat hardlink.txt
    cd ..

What to notice:

- `original.txt` and `hardlink.txt` have the same inode number.
- Editing through one name changes the same underlying data.
- Removing `original.txt` does not delete the data because `hardlink.txt` still points to the inode.

### Lab 2: Create And Inspect A Soft Link

Start from the same parent directory.

    mkdir lab2
    cd lab2
    echo "LFCS soft link practice" > target.txt
    ln -s target.txt shortcut.txt
    ls -l
    readlink shortcut.txt
    cat shortcut.txt
    echo "Added through shortcut" >> shortcut.txt
    cat target.txt
    rm target.txt
    ls -l
    cat shortcut.txt
    cd ..

What to notice:

- `shortcut.txt` points to the path `target.txt`.
- When `target.txt` exists, the shortcut works.
- After removing `target.txt`, the symlink becomes broken.
- The final `cat shortcut.txt` should fail because the target no longer exists.

### Lab 3: Compare Hard Links And Soft Links

Start from the same parent directory.

    mkdir lab3
    cd lab3
    echo "Same content, different link behavior" > file.txt
    ln file.txt hard.txt
    ln -s file.txt soft.txt
    ls -li
    ls -l
    cat hard.txt
    cat soft.txt
    rm file.txt
    ls -li
    ls -l
    cat hard.txt
    cat soft.txt
    cd ..

What to notice:

- `file.txt` and `hard.txt` share the same inode.
- `soft.txt` has its own inode because it is a separate symlink file.
- After removing `file.txt`, `hard.txt` still works.
- After removing `file.txt`, `soft.txt` breaks because it points to the missing path.

## Cleanup

Run this from the same parent directory where `lab1`, `lab2`, and `lab3` were created.

    rm -r lab1
    rm -r lab2
    rm -r lab3

These commands only remove the lab directories and the files created inside them.

## Recap Checklist

- [ ] I can explain that an inode points to file data.
- [ ] I can explain that a hard link is another filename for the same inode.
- [ ] I can explain that a soft link points to a path.
- [ ] I can create a hard link with `ln target link_name`.
- [ ] I can create a soft link with `ln -s target link_name`.
- [ ] I can inspect inode numbers with `ls -li`.
- [ ] I can inspect symlink targets with `ls -l` and `readlink`.
- [ ] I understand why hard links survive when one filename is deleted.
- [ ] I understand why soft links break when their target path is removed.
- [ ] I know that hard links usually cannot link directories or cross filesystems.

## Flashcards

What is a hard link?;A hard link is another filename that points to the same inode as an existing file.;file-management
What is a soft link?;A soft link, or symbolic link, is a file that points to a path rather than directly to an inode.;file-management
What is an inode?;An inode is a filesystem structure that stores file metadata and points to the file's data blocks.;file-management
What command creates a hard link?;Use `ln target link_name` to create a hard link.;file-management
What command creates a soft link?;Use `ln -s target link_name` to create a soft link.;file-management
What does the `-s` option mean in the `ln` command?;The `-s` option tells `ln` to create a symbolic link instead of a hard link.;file-management
How can you view inode numbers for files?;Use `ls -i` or `ls -li` to view inode numbers.;file-management
How can you tell whether two files are hard links to the same data?;They have the same inode number when viewed with `ls -li`.;file-management
What happens if one hard link is deleted?;The data remains available as long as at least one hard link still points to the inode.;file-management
What happens if the target of a soft link is deleted?;The soft link becomes broken because it points to a path that no longer exists.;file-management
Can hard links usually be created for directories?;No, hard links usually cannot be created for directories.;file-management
Can hard links cross filesystems?;No, hard links must stay on the same filesystem.;file-management
Can soft links point to directories?;Yes, soft links can point to directories.;file-management
Can soft links cross filesystems?;Yes, soft links can point to paths on other filesystems.;file-management
What command shows the target path of a symlink?;Use `readlink link_name` to show the path a symlink points to.;file-management
