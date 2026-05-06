# Searching Files Using Grep

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

Tags: grep text-search

## Lesson Context

This lesson is based on LFCS fundamentals for searching text with `grep`. The matching course notes include basic examples for searching a word in a file, searching recursively, ignoring case, inverting matches, and showing line numbers.

## What `grep` Does

`grep` searches text and prints the lines that match what you are looking for.

Think of it like using Find in a document, but from the command line.

For example, if you have a large log file and want to find every line containing the word `error`, `grep` can pull those lines out quickly.

## Basic Command Structure

The basic structure is:

    grep [options] 'pattern' filename

Breakdown:

- `grep` is the command.
- `[options]` change how the search behaves.
- `'pattern'` is the text you want to search for.
- `filename` is the file you want to search inside.

Example:

    grep 'error' app.log

This means:

- Search inside `app.log`
- Look for the text `error`
- Print every matching line

## Why Use Single Quotes?

You should usually wrap the search pattern in single quotes:

    grep 'root' users.txt

Single quotes protect the pattern from being interpreted by the shell.

For simple words, this may not seem important. But once a pattern contains spaces or special characters, quotes help prevent confusing results.

Example:

    grep 'Failed password' auth.log

Without quotes, the shell may treat `Failed` and `password` as separate arguments.

## Case Sensitivity

By default, `grep` is case-sensitive.

That means these are different:

- `Error`
- `error`
- `ERROR`

Example:

    grep 'error' app.log

This matches:

    error loading file

But it does not match:

    Error loading file
    ERROR loading file

To ignore case, use `-i`:

    grep -i 'error' app.log

Now it matches `error`, `Error`, and `ERROR`.

## Understanding Output

When `grep` finds a match, it prints the entire line that contains the match.

Example file:

    system started
    error loading config
    system stopped

Command:

    grep 'error' app.log

Output:

    error loading config

If there are multiple matching lines, `grep` prints all of them.

If there are no matches, `grep` usually prints nothing. That does not mean the command failed; it means no matching lines were found.

## Useful Basic Options

### `-i`: Ignore Case

    grep -i 'error' app.log

Finds `error`, `Error`, `ERROR`, and other case variations.

### `-n`: Show Line Numbers

    grep -n 'error' app.log

Shows which line number contains the match.

This is useful when you need to edit or inspect a file later.

### `-v`: Show Lines That Do Not Match

    grep -v 'error' app.log

This does the opposite: it prints lines that do not contain `error`.

### `-r`: Search Recursively

    grep -r 'PermitRootLogin' /etc

Searches through files inside a directory and its subdirectories.

For now, understand the basic idea: `-r` searches through a folder tree.

## Common Pitfalls

- Forgetting quotes around patterns with spaces.
- Expecting `grep 'error'` to match `Error` or `ERROR` without using `-i`.
- Thinking no output always means an error. Usually, it means no match was found.
- Searching the wrong file or directory.

## Hands-On Labs

### Lab 1: Search for a Word in a File

Start from an empty practice parent directory.

    mkdir lab1
    cd lab1

Create a sample file:

    printf "system started\nuser login successful\nsystem stopped\n" > app.log

Search for `user`:

    grep 'user' app.log

Expected output:

    user login successful

Search for `error`:

    grep 'error' app.log

Expected result: no output, because the file does not contain `error`.

Return to the parent directory:

    cd ..

### Lab 2: Practice Case Sensitivity

    mkdir lab2
    cd lab2

Create a sample log file:

    printf "error opening file\nError saving file\nERROR closing file\nsystem ok\n" > app.log

Search using lowercase `error`:

    grep 'error' app.log

Expected output:

    error opening file

Now search without caring about case:

    grep -i 'error' app.log

Expected output:

    error opening file
    Error saving file
    ERROR closing file

Return to the parent directory:

    cd ..

### Lab 3: Use Line Numbers and Inverted Matches

    mkdir lab3
    cd lab3

Create a sample configuration file:

    printf "Port 22\nPermitRootLogin no\nPasswordAuthentication yes\nUseDNS no\n" > sshd_config_sample

Search for `PermitRootLogin` and show the line number:

    grep -n 'PermitRootLogin' sshd_config_sample

Expected output:

    2:PermitRootLogin no

Show lines that do not contain `no`:

    grep -v 'no' sshd_config_sample

Expected output:

    Port 22
    PasswordAuthentication yes

Return to the parent directory:

    cd ..

## Cleanup

Assume you are in the same parent directory where `lab1`, `lab2`, and `lab3` were created.

If you are still inside one of the lab directories, return to the parent directory first:

    cd ..

Remove the lab directories and all files created inside them:

    rm -r lab1
    rm -r lab2
    rm -r lab3

These commands only remove the lab directories created during this lesson.

## Recap Checklist

- [ ] `grep` searches text inside files.
- [ ] Basic structure: `grep [options] 'pattern' filename`
- [ ] Put search patterns in single quotes.
- [ ] By default, `grep` is case-sensitive.
- [ ] Use `-i` for case-insensitive searches.
- [ ] Use `-n` to show line numbers.
- [ ] Use `-v` to show lines that do not match.
- [ ] Use `-r` to search through directories recursively.
- [ ] If `grep` prints nothing, it may simply mean no match was found.

## Flashcards

What does grep do?;grep searches text and prints lines that match a pattern.;grep text-search
What is the basic grep command structure?;grep [options] 'pattern' filename;grep text-search
In grep syntax, what is the pattern?;The pattern is the text you want grep to search for.;grep text-search
Why should grep patterns often be wrapped in single quotes?;Single quotes help prevent the shell from interpreting spaces or special characters in the pattern.;grep text-search
Is grep case-sensitive by default?;Yes. By default, grep treats lowercase and uppercase letters as different.;grep text-search
Which grep option ignores case?;-i makes grep search without caring about uppercase or lowercase letters.;grep text-search
What does grep print when it finds a match?;It prints the entire line that contains the matching text.;grep text-search
What usually happens when grep finds no matches?;It usually prints no output.;grep text-search
Which grep option shows line numbers?;-n shows the line number for each matching line.;grep text-search
Which grep option shows lines that do not match the pattern?;-v inverts the match and shows lines that do not contain the pattern.;grep text-search
Which grep option searches through directories recursively?;-r searches through files in a directory and its subdirectories.;grep text-search
What does grep 'error' app.log do?;It searches app.log and prints lines containing the exact lowercase text error.;grep text-search
Why might grep 'error' not find a line containing Error?;Because grep is case-sensitive by default, and Error has a capital E.;grep text-search
What is a common mistake when searching for a phrase with grep?;Forgetting to quote the phrase, which can cause the shell to split it into separate arguments.;grep text-search
In LFCS work, why is grep important?;It helps quickly filter useful information from large files such as logs and configuration files.;grep text-search
