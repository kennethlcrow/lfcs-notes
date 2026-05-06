# SUID, SGID, and Sticky Bit

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

Tags: permissions special-permissions

## Overview

SUID, SGID, and the sticky bit are special Linux permissions.

Normal permissions control who can read, write, and execute a file or directory. Special permissions add extra behavior on top of those normal permissions.

These permissions are important for LFCS because they affect security, shared directories, and how programs run.

No dedicated Lesson 18 course note was found in the local LFCS folder, so this note is based on the provided lesson scope and LFCS fundamentals.

## Key Concepts and Definitions

### Normal Linux Permissions

Linux permissions are usually viewed with:

    ls -l

Example:

    -rwxr-xr-x 1 user user 1234 file.sh

Breakdown:

    -      regular file
    rwx    owner can read, write, execute
    r-x    group can read and execute
    r-x    others can read and execute

The three permission groups are:

- owner
- group
- others

### SUID

SUID means Set User ID.

Normally, when you run a program, it runs with your user permissions.

When SUID is set on an executable file, the program runs with the permissions of the file owner.

Common example:

    /usr/bin/passwd

Regular users can change their own passwords with `passwd`, even though password information is stored in protected system files. SUID allows the command to do the specific privileged task it was designed for.

SUID appears as an `s` in the owner execute position:

    -rwsr-xr-x

### SGID

SGID means Set Group ID.

When SGID is set on an executable file, the file runs with the permissions of the group that owns the file.

SGID appears as an `s` in the group execute position:

    -rwxr-sr-x

SGID is also commonly used on directories.

When SGID is set on a directory, new files created inside that directory inherit the directory's group ownership. This is useful for shared team directories.

### Sticky Bit

The sticky bit is most commonly used on directories.

When the sticky bit is set on a shared writable directory, users can create files there if permissions allow it, but they cannot delete or rename files owned by other users.

Only these users can delete or rename a file inside a sticky directory:

- the file owner
- the directory owner
- root

Common example:

    /tmp

The sticky bit appears as a `t` in the others execute position:

    drwxrwxrwt

## Command Structure Breakdown

### View Permissions

Command structure:

    ls -l [file]

Parts:

- `ls` lists files.
- `-l` shows long format permissions.
- `[file]` is the file to inspect.

Example:

    ls -l /usr/bin/passwd

For directories, use:

    ls -ld [directory]

Parts:

- `-d` shows the directory itself instead of listing its contents.

Example:

    ls -ld /tmp

### Set SUID

Command structure:

    chmod u+s [file]

Parts:

- `chmod` changes permissions.
- `u+s` adds SUID to the user/owner permission area.
- `[file]` is the target file.

Numeric form:

    chmod 4XXX [file]

The leading `4` means SUID.

Example:

    chmod 4755 program

### Set SGID

Command structure:

    chmod g+s [file-or-directory]

Parts:

- `chmod` changes permissions.
- `g+s` adds SGID to the group permission area.
- `[file-or-directory]` is the target.

Numeric form:

    chmod 2XXX [file-or-directory]

The leading `2` means SGID.

Example:

    chmod 2755 shared

### Set Sticky Bit

Command structure:

    chmod +t [directory]

Parts:

- `chmod` changes permissions.
- `+t` adds the sticky bit.
- `[directory]` is the target directory.

Numeric form:

    chmod 1XXX [directory]

The leading `1` means sticky bit.

Example:

    chmod 1777 shared-dropbox

## Clean Examples

View SUID on the `passwd` command:

    ls -l /usr/bin/passwd

Set SUID on a file:

    chmod u+s demo-suid

Remove SUID from a file:

    chmod u-s demo-suid

Set SGID on a directory:

    chmod g+s shared

Remove SGID from a directory:

    chmod g-s shared

Set sticky bit on a directory:

    chmod +t dropbox

Remove sticky bit from a directory:

    chmod -t dropbox

Use numeric permissions:

    chmod 4755 file
    chmod 2755 directory
    chmod 1777 directory

## Special Permission Symbols

SUID:

    -rwsr-xr-x

The `s` is in the owner execute position.

SGID:

    -rwxr-sr-x

The `s` is in the group execute position.

Sticky bit:

    drwxrwxrwt

The `t` is in the others execute position.

Uppercase letters can also appear:

    S    special bit is set, but execute is missing
    T    sticky bit is set, but execute is missing

For the first pass, focus mainly on lowercase `s` and `t`.

## Use Cases and Best Practices

SUID is useful when a normal user must run a specific trusted program with the file owner's permissions.

Common example:

    passwd

Be careful with SUID, especially on root-owned files. A poorly designed SUID program can become a security risk.

SGID is useful for shared directories where files should stay associated with a team group.

Sticky bit is useful for shared writable directories where users should not delete or rename each other's files.

Common example:

    /tmp

## Hands-On Labs

Assume you are starting from an empty parent directory where `lab1`, `lab2`, and `lab3` will be created.

### Lab 1: View and Set SUID on a File

Step 1: Create and enter the lab directory.

    mkdir lab1
    cd lab1

Step 2: Create a simple file.

    touch demo-suid

Step 3: Give the file normal executable permissions.

    chmod 755 demo-suid

Step 4: View the file permissions.

    ls -l demo-suid

Step 5: Set the SUID bit.

    chmod u+s demo-suid

Step 6: View the permissions again.

    ls -l demo-suid

Step 7: Notice the owner execute area.

You should see an `s` in the owner execute position, like this:

    -rwsr-xr-x

Step 8: Remove the SUID bit.

    chmod u-s demo-suid

Step 9: View the permissions again.

    ls -l demo-suid

What you practiced:

- setting SUID with `chmod u+s`
- removing SUID with `chmod u-s`
- recognizing SUID with `ls -l`

### Lab 2: Set SGID on a Directory

Start from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

Step 1: If you are still inside `lab1`, return to the parent directory.

    cd ..

Step 2: Create and enter the lab directory.

    mkdir lab2
    cd lab2

Step 3: Create a shared directory.

    mkdir shared

Step 4: View the directory permissions.

    ls -ld shared

Step 5: Set the SGID bit on the directory.

    chmod g+s shared

Step 6: View the directory permissions again.

    ls -ld shared

Step 7: Notice the group execute area.

You should see an `s` in the group execute position, like this:

    drwxr-sr-x

Step 8: Create a file inside the shared directory.

    touch shared/team-note.txt

Step 9: List the file and directory.

    ls -ld shared
    ls -l shared/team-note.txt

Step 10: Remove the SGID bit.

    chmod g-s shared

Step 11: View the directory permissions again.

    ls -ld shared

What you practiced:

- setting SGID with `chmod g+s`
- removing SGID with `chmod g-s`
- recognizing SGID with `ls -ld`

### Lab 3: Set Sticky Bit on a Shared Directory

Start from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

Step 1: If you are still inside `lab2`, return to the parent directory.

    cd ..

Step 2: Create and enter the lab directory.

    mkdir lab3
    cd lab3

Step 3: Create a shared dropbox directory.

    mkdir dropbox

Step 4: Give everyone read, write, and execute permissions on the directory.

    chmod 777 dropbox

Step 5: View the directory permissions.

    ls -ld dropbox

Step 6: Set the sticky bit.

    chmod +t dropbox

Step 7: View the directory permissions again.

    ls -ld dropbox

Step 8: Notice the final permission character.

You should see a `t` at the end, like this:

    drwxrwxrwt

Step 9: Create a file inside the directory.

    touch dropbox/my-temp-file.txt

Step 10: List the directory contents.

    ls -l dropbox

Step 11: Remove the sticky bit.

    chmod -t dropbox

Step 12: View the directory permissions again.

    ls -ld dropbox

What you practiced:

- setting sticky bit with `chmod +t`
- removing sticky bit with `chmod -t`
- recognizing sticky bit with `ls -ld`

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

Step 7: Remove `lab3` and everything created inside it.

    rm -r lab3

Step 8: List the current directory to confirm the lab directories are gone.

    ls -la

No users, groups, or services were created during these labs.

## Notes From Follow-Up Questions

No follow-up clarifications were added for this topic yet.

## Recap Checklist

- [ ] I know that SUID, SGID, and sticky bit are special Linux permissions.
- [ ] I know SUID lets a file run with the permissions of the file owner.
- [ ] I know SGID lets a file run with the permissions of the file's group.
- [ ] I know SGID on a directory helps new files inherit the directory's group.
- [ ] I know sticky bit is commonly used on shared directories like `/tmp`.
- [ ] I know sticky bit prevents users from deleting or renaming files they do not own in a shared directory.
- [ ] I can view special permissions with `ls -l` and `ls -ld`.
- [ ] I can recognize SUID by `s` in the owner execute position.
- [ ] I can recognize SGID by `s` in the group execute position.
- [ ] I can recognize sticky bit by `t` in the others execute position.
- [ ] I know numeric `4` means SUID.
- [ ] I know numeric `2` means SGID.
- [ ] I know numeric `1` means sticky bit.
- [ ] I know to be careful with SUID, especially on root-owned files.

## Flashcards

What are SUID, SGID, and sticky bit?;They are special Linux permissions that add extra behavior on top of normal read, write, and execute permissions.;permissions special-permissions
What does SUID stand for?;SUID stands for Set User ID.;permissions special-permissions
What does SUID do on an executable file?;It allows the file to run with the permissions of the file owner.;permissions special-permissions
Where does the SUID symbol appear in `ls -l` output?;It appears as `s` in the owner execute position, such as `-rwsr-xr-x`.;permissions special-permissions
What is a common example of a SUID program?;`/usr/bin/passwd` is a common SUID program because it lets users change their own passwords safely.;permissions special-permissions
What does SGID stand for?;SGID stands for Set Group ID.;permissions special-permissions
What does SGID do on an executable file?;It allows the file to run with the permissions of the group that owns the file.;permissions special-permissions
Where does the SGID symbol appear in `ls -l` output?;It appears as `s` in the group execute position, such as `-rwxr-sr-x`.;permissions special-permissions
What does SGID do on a directory?;It causes new files created inside the directory to inherit the directory's group ownership.;permissions special-permissions
Why is SGID useful on shared directories?;It helps files created by different users stay associated with the shared team group.;permissions special-permissions
What does the sticky bit do on a directory?;It prevents users from deleting or renaming files owned by other users inside a shared writable directory.;permissions special-permissions
Where does the sticky bit symbol appear in `ls -ld` output?;It appears as `t` in the others execute position, such as `drwxrwxrwt`.;permissions special-permissions
What is a common example of a sticky bit directory?;`/tmp` commonly uses the sticky bit because many users can write there.;permissions special-permissions
What command sets SUID symbolically?;`chmod u+s [file]` sets SUID on a file.;permissions special-permissions
What command sets SGID symbolically?;`chmod g+s [file-or-directory]` sets SGID.;permissions special-permissions
What command sets the sticky bit symbolically?;`chmod +t [directory]` sets the sticky bit on a directory.;permissions special-permissions
What leading numeric value represents SUID?;The leading numeric value `4` represents SUID.;permissions special-permissions
What leading numeric value represents SGID?;The leading numeric value `2` represents SGID.;permissions special-permissions
What leading numeric value represents the sticky bit?;The leading numeric value `1` represents the sticky bit.;permissions special-permissions
Why should SUID be used carefully?;SUID can allow programs to run with elevated owner permissions, so unsafe SUID programs can create security risks.;permissions special-permissions
