# Searching For Files

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

## Context

This lesson covers the LFCS basics of searching for files in Linux, mainly with the `find` command.

Searching for files is important because Linux systems can contain thousands of files spread across many directories. As an administrator, you may need to find configuration files, logs, large files, recently changed files, or files of a specific type.

The matching LFCS course notes found for this topic were in `Essential Commands.md`, especially the section on finding files. The key course idea is:

    find LOCATION PARAMETERS

In plain English:

    Start searching in this place, then apply these search rules.

## Key Concepts

### File Searching

File searching means locating files or directories based on criteria such as name, type, size, or modification time.

### The `find` Command

The `find` command searches through a directory tree.

It starts at the location you give it, then checks that location and everything below it.

### Search Location

The location tells `find` where to begin.

Examples:

    .
    /home
    /var
    /etc

Common meanings:

- `.` means the current directory
- `/` means the root of the whole filesystem
- `/home` means start searching in user home directories

### Search Criteria

Search criteria tell `find` what kind of results you want.

Examples:

    -name notes.txt
    -type f
    -size +10M
    -mmin -10

## Command Structure Breakdown

Basic structure:

    find [location] [search-criteria]

Example:

    find . -name notes.txt

Breakdown:

- `find` is the command
- `.` is the location to search from
- `-name` means search by file name
- `notes.txt` is the name to search for

Memory hook:

    find LOCATION PARAMETERS

First tell Linux where to search. Then tell Linux what to search for.

## Searching By Name

Search for a file named `notes.txt` in the current directory and below:

    find . -name notes.txt

Search under `/home`:

    find /home -name notes.txt

The `-name` option is case-sensitive.

This matches:

    notes.txt

But not:

    Notes.txt

## Case-Insensitive Name Search

Use `-iname` to ignore capitalization:

    find . -iname notes.txt

This can match:

    notes.txt
    Notes.txt
    NOTES.TXT

## Searching By File Type

Find regular files:

    find . -type f

Find directories:

    find . -type d

Breakdown:

- `-type` means search by object type
- `f` means regular file
- `d` means directory

Examples of regular files:

    notes.txt
    image.jpg
    app.log

Examples of directories:

    documents
    pictures
    logs

## Searching By Extension

Find files ending in `.txt`:

    find . -name "*.txt"

Find files ending in `.jpg`:

    find . -name "*.jpg"

Find files ending in `.log`:

    find . -name "*.log"

The quotes around `"*.txt"` are important. They help make sure the pattern is passed to `find` instead of being expanded too early by the shell.

Pattern idea:

- `*` means anything
- `"*.txt"` means anything ending in `.txt`

## Searching By Size

Find files larger than 10 MB:

    find . -size +10M

Find files larger than 20 GB:

    find . -size +20G

Breakdown:

- `-size` means search by size
- `+10M` means larger than 10 megabytes
- `+20G` means larger than 20 gigabytes

Common size units:

- `k` means kilobytes
- `M` means megabytes
- `G` means gigabytes

Important signs:

- `+10M` means larger than 10 MB
- `-10M` means smaller than 10 MB
- `10M` means approximately exactly 10 MB

For LFCS basics, the most common practical use is finding large files:

    find . -type f -size +100M

## Finding Recently Modified Files

Linux tracks when files are modified. Modified means the file contents changed.

Find files modified in the last 10 minutes:

    find . -mmin -10

Breakdown:

- `-mmin` means modified time in minutes
- `-10` means less than 10 minutes ago

Find files modified more than 10 minutes ago:

    find . -mmin +10

Find files modified in the last day:

    find . -mtime -1

Breakdown:

- `-mtime` means modified time in days
- `-1` means less than 1 day ago

Beginner memory:

- `mmin` is minutes
- `mtime` is days

## Combining Search Parameters

You can combine multiple criteria in one command.

Find regular `.log` files:

    find . -type f -name "*.log"

Find regular files larger than 10 MB:

    find . -type f -size +10M

Find `.txt` files modified in the last 10 minutes:

    find . -type f -name "*.txt" -mmin -10

Read the command like a sentence:

    Find, starting here, regular files, with this name pattern, changed recently.

## Practical Scenarios

### Disk Space Management

Find large files under `/var`:

    find /var -type f -size +100M

This can help locate large logs or cache files.

### File Audit

Find files under `/etc` modified in the last hour:

    find /etc -type f -mmin -60

This can help review recent configuration changes.

### Image Search

Find `.jpg` files under `/home`:

    find /home -type f -name "*.jpg"

This can help locate user image files.

## Hands-On Labs

Assume you are starting from an empty practice parent directory.

Each lab is independent. Begin each lab from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

## Lab 1: Search By Name And Type

Goal: Create files and directories, then search for them by name and type.

Step 1: Create and enter the lab directory.

    mkdir lab1
    cd lab1

Step 2: Create some files.

    touch notes.txt
    touch report.txt
    touch image.jpg

Step 3: Create some directories.

    mkdir documents
    mkdir pictures

Step 4: Search for one exact file name.

    find . -name notes.txt

Step 5: Search for all `.txt` files.

    find . -name "*.txt"

Step 6: Search only for regular files.

    find . -type f

Step 7: Search only for directories.

    find . -type d

Step 8: Return to the parent directory.

    cd ..

## Lab 2: Search By Extension And Size

Goal: Create files with different extensions and sizes, then find matching files.

Step 1: Create and enter the lab directory.

    mkdir lab2
    cd lab2

Step 2: Create directories.

    mkdir logs
    mkdir images

Step 3: Create small files.

    touch logs/app.log
    touch logs/error.log
    touch images/photo.jpg
    touch notes.txt

Step 4: Create a file larger than 1 MB.

    dd if=/dev/zero of=large-file.bin bs=1M count=2

Step 5: Find `.log` files.

    find . -name "*.log"

Step 6: Find `.jpg` files.

    find . -name "*.jpg"

Step 7: Find files larger than 1 MB.

    find . -type f -size +1M

Step 8: Return to the parent directory.

    cd ..

## Lab 3: Search By Recent Modification Time

Goal: Find files that were recently modified.

Step 1: Create and enter the lab directory.

    mkdir lab3
    cd lab3

Step 2: Create a directory for test files.

    mkdir testfiles

Step 3: Create two files.

    touch testfiles/old.txt
    touch testfiles/new.txt

Step 4: Set one file's timestamp to 2 days ago.

    touch -d "2 days ago" testfiles/old.txt

Step 5: Update the other file so it is modified now.

    echo "new content" > testfiles/new.txt

Step 6: Find files modified in the last 10 minutes.

    find . -type f -mmin -10

Step 7: Find `.txt` files modified in the last 10 minutes.

    find . -type f -name "*.txt" -mmin -10

Step 8: Find files modified more than 1 day ago.

    find . -type f -mtime +1

Step 9: Return to the parent directory.

    cd ..

## Cleanup

Assume you are in the same parent directory where `lab1`, `lab2`, and `lab3` were created.

Step 1: If you are still inside a lab directory, return to the parent directory.

    cd ..

Step 2: Remove Lab 1 and everything created inside it.

    rm -r lab1

Step 3: Remove Lab 2 and everything created inside it.

    rm -r lab2

Step 4: Remove Lab 3 and everything created inside it.

    rm -r lab3

No users, groups, or services were created in these labs.

## Recap Checklist

- [ ] I understand the basic structure: `find [location] [criteria]`
- [ ] I know that `.` means the current directory
- [ ] I can search by exact name with `-name`
- [ ] I can search case-insensitively with `-iname`
- [ ] I can search for files with `-type f`
- [ ] I can search for directories with `-type d`
- [ ] I can search by extension using patterns like `"*.txt"`
- [ ] I can search by size with `-size +10M`
- [ ] I can search by recent modification time with `-mmin`
- [ ] I can combine search criteria in one `find` command

## Flashcards

What is the basic structure of the find command?;`find [location] [search-criteria]`;file-search find
What does the location part of a find command tell Linux?;It tells `find` where to start searching.;file-search find
What does `.` mean in `find . -name notes.txt`?;It means the current directory.;file-search find
What does `find . -name notes.txt` do?;It searches from the current directory for files or directories named `notes.txt`.;file-search find
What is the difference between `-name` and `-iname`?;`-name` is case-sensitive, while `-iname` ignores capitalization.;file-search find
What does `find . -type f` search for?;It searches for regular files under the current directory.;file-search find
What does `find . -type d` search for?;It searches for directories under the current directory.;file-search find
What does the `*` mean in `"*.txt"`?;It means anything, so `"*.txt"` matches names ending in `.txt`.;file-search find
Why should patterns like `"*.log"` usually be quoted?;Quoting helps pass the pattern to `find` instead of letting the shell expand it early.;file-search find
What does `find . -size +10M` search for?;It searches for items larger than 10 megabytes under the current directory.;file-search find
What does the plus sign mean in `-size +10M`?;It means larger than the given size.;file-search find
What does `-mmin` search by?;It searches by modification time measured in minutes.;file-search find
What does `find . -mmin -10` show?;It shows files modified less than 10 minutes ago.;file-search find
What does `-mtime` search by?;It searches by modification time measured in days.;file-search find
How can you find regular `.log` files under the current directory?;Use `find . -type f -name "*.log"`.;file-search find
