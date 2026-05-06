# Setting, Listing, And Changing Standard File Permissions

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

## Topic

How to set, list, and change standard file permissions.

## LFCS Focus

This note covers the fundamentals of Linux file permissions:

- Read, write, and execute permissions
- Owner, group, and others
- Reading permission output with `ls -l`
- Changing permissions with `chmod`
- Symbolic and numeric permission modes
- Basic awareness of SUID, SGID, and the sticky bit
- Safe permission habits for common files and directories

## Core Idea

Linux uses permissions to decide who can do what with a file or directory.

The three basic permissions are:

| Permission | Symbol | On a file | On a directory |
|---|---|---|---|
| Read | `r` | View file contents | List directory contents |
| Write | `w` | Change file contents | Create, delete, or rename files inside |
| Execute | `x` | Run the file as a program or script | Enter the directory with `cd` |

A simple way to remember them:

- `r` means you can look inside.
- `w` means you can change it.
- `x` means you can run it, or enter it if it is a directory.

For directories, execute permission is especially important. A directory without `x` permission cannot be entered with `cd`.

## User Categories

Every file and directory has permissions for three categories:

| Category | Symbol | Meaning |
|---|---|---|
| User / owner | `u` | The user who owns the file |
| Group | `g` | Users who belong to the file's group |
| Others | `o` | Everyone else |
| All | `a` | User, group, and others together |

Permissions are shown in this order:

    owner group others

Example:

    rw- r-- r--

Meaning:

- Owner can read and write.
- Group can read.
- Others can read.

## Listing Permissions With ls -l

Command structure:

    ls -l [file-or-directory]

Breakdown:

    ls       list files
    -l       use long listing format
    [target] optional file or directory to inspect

Example:

    ls -l file.txt

Example output:

    -rw-r--r-- 1 student student 20 May 6 10:00 file.txt

The permission section is:

    -rw-r--r--

Breakdown:

    -  rw-  r--  r--
    |   |    |    |
    |   |    |    others
    |   |    group
    |   owner
    file type

The first character shows the file type:

| Character | Meaning |
|---|---|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |

So this:

    -rw-r--r--

Means:

- Regular file
- Owner can read and write
- Group can read
- Others can read

This:

    drwxr-xr-x

Means:

- Directory
- Owner can read, write, and enter
- Group can read and enter
- Others can read and enter

## Changing Permissions With chmod

The main command for changing permissions is `chmod`.

Command structure:

    chmod [permission-change] [file-or-directory]

Example:

    chmod u+x script.sh

Breakdown:

    chmod       change permissions
    u           user / owner
    +           add permission
    x           execute permission
    script.sh   target file

There are two common ways to use `chmod`:

- Symbolic mode
- Numeric mode

## Symbolic Mode

Symbolic mode uses letters.

User categories:

    u = user / owner
    g = group
    o = others
    a = all

Actions:

    + = add permission
    - = remove permission
    = = set exact permission

Permissions:

    r = read
    w = write
    x = execute

Examples:

    chmod u+x script.sh

Adds execute permission for the owner.

    chmod g-w file.txt

Removes write permission from the group.

    chmod o=r file.txt

Sets others to read-only.

    chmod a+x script.sh

Adds execute permission for everyone.

Important distinction:

- `+` adds a permission.
- `-` removes a permission.
- `=` replaces the whole permission set for that category.

So this:

    chmod o=r file.txt

Means others get exactly read permission, with no write and no execute.

## Numeric Mode

Numeric mode uses numbers.

Each permission has a value:

| Permission | Value |
|---|---|
| Read | `4` |
| Write | `2` |
| Execute | `1` |

You add the numbers together:

| Number | Meaning |
|---|---|
| `7` | `rwx` |
| `6` | `rw-` |
| `5` | `r-x` |
| `4` | `r--` |
| `0` | `---` |

Numeric mode uses three digits:

    owner group others

Example:

    chmod 644 file.txt

Breakdown:

    6 = owner: read + write
    4 = group: read only
    4 = others: read only

So `644` means:

    rw-r--r--

Common basic permission modes:

| Mode | Meaning | Common use |
|---|---|---|
| `644` | Owner read/write, group read, others read | Normal files |
| `600` | Owner read/write only | Private files and keys |
| `755` | Owner full access, group read/execute, others read/execute | Directories and scripts |
| `700` | Owner full access only | Private directories |

## Permission Best Practices

Good default habits:

- Use `644` for normal files that others may read.
- Use `600` for private files, secrets, and private keys.
- Use `755` for normal directories and scripts that others may access or run.
- Use `700` for private directories.
- Avoid giving write permission to others unless there is a clear reason.
- Remember that scripts need execute permission before they can be run directly.
- Remember that directories need execute permission before users can enter them.

Real-world examples:

    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys
    chmod 600 ~/.ssh/id_ed25519

Private SSH files must be locked down or SSH may refuse key-based authentication.

## Special Permissions: Basic Awareness

SUID, SGID, and the sticky bit are special permissions beyond normal `rwx`.

For the first pass, know only the basic ideas:

| Special permission | Basic idea |
|---|---|
| SUID | Runs a file with the file owner's privileges |
| SGID | Runs with the group's privileges, or makes new files inherit a directory's group |
| Sticky bit | Common on shared directories; users can usually delete only their own files |

Example:

    ls -ld /tmp

You may see output like:

    drwxrwxrwt

The final `t` shows the sticky bit.

For now, the foundation is normal `rwx` permissions.

## Hands-On Labs

Before starting, create a practice parent directory:

    mkdir permissions-practice
    cd permissions-practice

Each lab starts from this same parent directory and is independent.

## Lab 1: List And Read Basic File Permissions

Goal: create a file, list its permissions, and understand the output.

Start from the parent directory where all labs will be created.

    mkdir lab1
    cd lab1
    pwd
    echo "Hello LFCS" > notes.txt
    ls -l
    ls -l notes.txt
    cat notes.txt

Notice:

- `notes.txt` exists.
- `ls -l` shows the permission string.
- `cat` works when you have read permission.

Remove your own read permission:

    chmod u-r notes.txt
    ls -l notes.txt
    cat notes.txt

The `cat` command may fail because the owner no longer has read permission.

Restore read permission:

    chmod u+r notes.txt
    ls -l notes.txt
    cat notes.txt

Return to the parent directory:

    cd ..

## Lab 2: Change Permissions With Symbolic Mode

Goal: use `chmod` with `u`, `g`, `o`, `+`, `-`, and `=`.

Start from the parent directory where all labs will be created.

    mkdir lab2
    cd lab2
    pwd
    echo "echo Hello from script" > hello.sh
    ls -l hello.sh

Try to run the script directly:

    ./hello.sh

It may fail because the file does not have execute permission yet.

Add execute permission for the owner:

    chmod u+x hello.sh
    ls -l hello.sh
    ./hello.sh

Remove execute permission from the owner:

    chmod u-x hello.sh
    ls -l hello.sh

Set exact permissions for others to read-only:

    chmod o=r hello.sh
    ls -l hello.sh

Add execute permission for everyone:

    chmod a+x hello.sh
    ls -l hello.sh
    ./hello.sh

Return to the parent directory:

    cd ..

## Lab 3: Change Permissions With Numeric Mode

Goal: use numeric permissions like `644`, `600`, `755`, and `700`.

Start from the parent directory where all labs will be created.

    mkdir lab3
    cd lab3
    pwd
    echo "This is a normal file" > normal.txt
    echo "This is private" > private.txt
    echo "echo Numeric permissions work" > runme.sh
    mkdir private_dir
    ls -l

Set a normal file to `644`:

    chmod 644 normal.txt
    ls -l normal.txt

Meaning:

    owner: read/write
    group: read
    others: read

Set a private file to `600`:

    chmod 600 private.txt
    ls -l private.txt

Meaning:

    owner: read/write
    group: no access
    others: no access

Set a script to `755`:

    chmod 755 runme.sh
    ls -l runme.sh
    ./runme.sh

Meaning:

    owner: read/write/execute
    group: read/execute
    others: read/execute

Set a private directory to `700`:

    chmod 700 private_dir
    ls -ld private_dir

Meaning:

    owner: read/write/enter
    group: no access
    others: no access

Return to the parent directory:

    cd ..

## Cleanup

Assume you are in the same parent directory where `lab1`, `lab2`, and `lab3` were created.

Check where you are:

    pwd
    ls

Remove Lab 1 files and directory:

    rm -r lab1

Remove Lab 2 files and directory:

    rm -r lab2

Remove Lab 3 files and directory:

    rm -r lab3

Confirm cleanup:

    ls

This cleanup only removes:

    lab1
    lab2
    lab3

No users, groups, or services were created in these labs.

## Recap Checklist

- `ls -l` shows detailed permissions.
- The first character shows file type: `-`, `d`, or `l`.
- Permissions are grouped as owner, group, and others.
- `r` means read.
- `w` means write.
- `x` means execute for files and enter for directories.
- `chmod` changes permissions.
- Symbolic mode uses letters like `u+x`, `g-w`, and `o=r`.
- Numeric mode uses values: `r=4`, `w=2`, `x=1`.
- `644` is common for normal files.
- `600` is common for private files.
- `755` is common for scripts and normal directories.
- `700` is common for private directories.
- SUID, SGID, and sticky bit are special permissions, but normal `rwx` permissions are the foundation.

## Flashcards

What command lists files with detailed permission information?;`ls -l` lists files in long format, including file type, permissions, owner, group, size, and timestamp.;permissions
In `ls -l` output, what does the first character of the permission string show?;It shows the file type, such as `-` for a regular file, `d` for a directory, or `l` for a symbolic link.;permissions
What are the three standard Linux permission types?;The three standard permissions are read (`r`), write (`w`), and execute (`x`).;permissions
What does read permission mean on a regular file?;Read permission on a regular file allows you to view the file's contents.;permissions
What does write permission mean on a directory?;Write permission on a directory allows you to create, delete, or rename files inside that directory.;permissions
What does execute permission mean on a directory?;Execute permission on a directory allows you to enter it with `cd` and access items inside if their names are known.;permissions
What are the three user categories for Linux permissions?;The categories are user/owner (`u`), group (`g`), and others (`o`).;permissions
What does `chmod u+x script.sh` do?;It adds execute permission for the file owner on `script.sh`.;permissions
What does `chmod g-w file.txt` do?;It removes write permission from the group on `file.txt`.;permissions
What does `chmod o=r file.txt` do?;It sets permissions for others to exactly read-only on `file.txt`.;permissions
In numeric permissions, what values represent read, write, and execute?;Read is `4`, write is `2`, and execute is `1`.;permissions
What permission string does numeric mode `644` represent?;`644` represents `rw-r--r--`: owner can read/write, group can read, and others can read.;permissions
What is `600` commonly used for?;`600` is commonly used for private files because only the owner can read and write them.;permissions
What is `755` commonly used for?;`755` is commonly used for directories and scripts where the owner needs full access and others need read/execute access.;permissions
Why do scripts need execute permission?;Scripts need execute permission to be run directly, such as with `./script.sh`.;permissions
What is the basic purpose of the sticky bit?;The sticky bit is commonly used on shared directories so users can usually delete only their own files.;permissions
