# Analyzing Text Using Basic Regular Expressions

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

## Context

This note covers LFCS fundamentals for analyzing text using basic regular expressions. The local LFCS notes include related `grep` basics in Essential Commands, but no dedicated Lesson 24 note was found. This lesson stays focused on beginner-level regex concepts used with Linux text-search tools.

## What Regular Expressions Are

A regular expression, often shortened to regex, is a search pattern.

Instead of searching only for one exact word, regex lets you describe a pattern of text.

Simple search:

    grep 'error' file.txt

This finds lines containing the exact text `error`.

Regex search:

    grep '^error' file.txt

This finds lines where `error` appears at the start of the line.

Think of regex as a way to tell Linux:

- Find lines that start with this
- Find lines that end with this
- Find any single character here
- Find repeated characters
- Find one of these options
- Find text that looks like an IP address

In LFCS work, regex is useful when reading configuration files, logs, command output, and system data.

## Command Structure

Basic command structure:

    grep 'pattern' file

Breakdown:

    grep       command used to search text
    'pattern'  the text or regex pattern to search for
    file       the file to search inside

Useful basic options:

    grep -n 'pattern' file

Shows matching lines with line numbers.

    grep -i 'pattern' file

Searches without caring about uppercase or lowercase.

    grep -v 'pattern' file

Shows lines that do not match.

    grep -E 'pattern' file

Uses extended regular expressions, which makes symbols like `+`, `?`, `{}`, and `|` easier to use.

Beginner note:

- `grep` by itself uses basic regular expressions.
- `grep -E` uses extended regular expressions.
- Use plain `grep` for simple patterns like `^`, `$`, `.`, `*`, and `[]`.
- Use `grep -E` when practicing `+`, `?`, `{}`, and `|`.

## Key Concepts and Definitions

### Simple Text Search

A normal search pattern looks for exact text:

    grep 'root' users.txt

Meaning:

    Find every line in users.txt that contains root anywhere on the line.

### `^` Start of Line

The caret means the line must start with this pattern.

    grep '^server' file.txt

Matches:

    server01
    server-name

Does not match:

    myserver
    backup server

### `$` End of Line

The dollar sign means the line must end with this pattern.

    grep 'log$' file.txt

Matches:

    auth.log
    system.log

Does not match:

    logfile
    auth.log.old

### `.` Any One Character

The period means match any single character.

    grep 's.t' file.txt

Matches:

    sat
    set
    s1t
    s-t

Does not match:

    st
    seat

The period stands for exactly one character.

### `*` Zero or More

The star means repeat the previous character zero or more times.

    grep 'lo*g' file.txt

Matches:

    lg
    log
    loog
    looog

In `lo*g`, the `*` applies only to the `o`.

### `[]` Character Set

Square brackets mean match one character from this set.

    grep 'gr[ae]y' file.txt

Matches:

    gray
    grey

Does not match:

    groy

Character ranges:

    grep '[0-9]' file.txt

Matches lines containing at least one digit.

    grep '[a-z]' file.txt

Matches lines containing at least one lowercase letter.

### `+` One or More

With `grep -E`, plus means one or more of the previous character or pattern.

    grep -E 'lo+g' file.txt

Matches:

    log
    loog
    looog

Does not match:

    lg

Difference:

    * means zero or more
    + means one or more

### `?` Optional

With `grep -E`, question mark means the previous character or pattern is optional.

    grep -E 'colou?r' file.txt

Matches:

    color
    colour

The `u` is optional.

### `{}` Specific Number of Repetitions

With `grep -E`, braces let you specify how many times something repeats.

    grep -E '[0-9]{3}' file.txt

Matches lines containing three digits in a row.

Examples:

    123
    808
    999

### `|` Alternation

With `grep -E`, pipe means or.

    grep -E 'error|failed' file.txt

Matches lines containing:

    error

or:

    failed

This is useful when searching logs for several related words.

## Building More Useful Patterns

Regex becomes powerful when you combine small pieces.

Example:

    grep -E '^[0-9]{3}' file.txt

Breakdown:

    ^        start of line
    [0-9]    any digit
    {3}      exactly three times

Meaning:

    Find lines that start with three digits.

Another example:

    grep -E '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' file.txt

This is a beginner-friendly pattern for finding IP-address-like text.

Breakdown:

    [0-9]+   one or more digits
    \.       a literal period

The backslash matters because `.` by itself means any one character. To search for a real dot, use:

    \.

This pattern is not a perfect IP address validator, but it is useful for basic text searching practice.

## Hands-On Labs

Assume you are starting from an empty parent directory where you want `lab1`, `lab2`, and `lab3` created.

Each lab is independent.

## Lab 1: Search Lines by Beginning and End

Goal: Practice `^`, `$`, and normal `grep`.

Start from your parent lab directory.

    mkdir lab1
    cd lab1

Create a practice file:

    touch services.txt

Add sample text:

    printf "ssh enabled\nhttp enabled\ndns disabled\nssh disabled\nbackup ssh\ncron enabled\n" > services.txt

Show the file:

    cat services.txt

Find lines that start with `ssh`:

    grep '^ssh' services.txt

Find lines that end with `enabled`:

    grep 'enabled$' services.txt

Find lines that contain `ssh` anywhere:

    grep 'ssh' services.txt

Show line numbers for lines containing `enabled`:

    grep -n 'enabled' services.txt

Return to the parent directory:

    cd ..

What you learned:

- `^ssh` means starts with ssh
- `enabled$` means ends with enabled
- plain `ssh` means contains ssh anywhere

## Lab 2: Match Characters and Character Ranges

Goal: Practice `.`, `[]`, and digit matching.

Start from the same parent directory where `lab1` was created.

    mkdir lab2
    cd lab2

Create a practice file:

    touch names.txt

Add sample text:

    printf "cat\ncot\ncut\ncoat\ncast\nserver1\nserver2\nserverA\n" > names.txt

Show the file:

    cat names.txt

Find three-character words that match `c`, then any one character, then `t`:

    grep 'c.t' names.txt

Find lines containing `cat` or `cot` using a character set:

    grep 'c[ao]t' names.txt

Find lines containing a digit:

    grep '[0-9]' names.txt

Find lines containing `server` followed by one digit:

    grep 'server[0-9]' names.txt

Return to the parent directory:

    cd ..

What you learned:

- `.` matches any one character
- `[ao]` means a or o
- `[0-9]` means any digit from 0 through 9

## Lab 3: Use Extended Regex for Repetition and Either/Or

Goal: Practice `grep -E`, `+`, `?`, `{}`, and `|`.

Start from the same parent directory where `lab1` and `lab2` were created.

    mkdir lab3
    cd lab3

Create a practice file:

    touch logs.txt

Add sample text:

    printf "error disk full\nfailed login\nfail login\ncolor setting\ncolour setting\ncode 200\ncode 404\nip 192.168.1.10\nip 10.0.0.5\n" > logs.txt

Show the file:

    cat logs.txt

Find lines containing `error` or `failed`:

    grep -E 'error|failed' logs.txt

Find `color` and `colour`:

    grep -E 'colou?r' logs.txt

Find lines containing three digits in a row:

    grep -E '[0-9]{3}' logs.txt

Find IP-address-like text:

    grep -E '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' logs.txt

Return to the parent directory:

    cd ..

What you learned:

- `grep -E` enables easier extended regex syntax
- `|` means or
- `?` means optional
- `{3}` means exactly three repetitions
- `+` means one or more repetitions
- `\.` searches for a real period

## Cleanup

Run these commands from the same parent directory where you created `lab1`, `lab2`, and `lab3`.

If you are inside one of the lab directories, return to the parent directory first:

    cd ..

Remove Lab 1 files and directory:

    rm lab1/services.txt
    rmdir lab1

Remove Lab 2 files and directory:

    rm lab2/names.txt
    rmdir lab2

Remove Lab 3 files and directory:

    rm lab3/logs.txt
    rmdir lab3

No users, groups, or services were created in these labs.

## Recap Checklist

- Regex is a pattern language for searching text.
- `grep 'pattern' file` searches for matching lines.
- `^` matches the start of a line.
- `$` matches the end of a line.
- `.` matches any one character.
- `*` means zero or more of the previous character.
- `[]` matches one character from a set or range.
- `grep -E` makes extended regex easier to use.
- `+` means one or more.
- `?` means optional.
- `{}` controls the number of repetitions.
- `|` means or.
- Use `\.` when you want to match a real period.
- Regex is especially useful for logs, config files, command output, and data extraction.

## Flashcards

What is a regular expression?;A regular expression is a search pattern used to find text that matches a specific structure.;regex grep text-processing
What is the basic structure of a grep command?;The basic structure is grep 'pattern' file, where grep searches, the pattern describes what to find, and the file is searched.;regex grep text-processing
What does ^ mean in a regular expression?;^ matches the start of a line.;regex grep text-processing
What does $ mean in a regular expression?;$ matches the end of a line.;regex grep text-processing
What does . mean in a regular expression?;. matches any single character.;regex grep text-processing
What does * mean in a regular expression?;* means zero or more repetitions of the previous character or pattern.;regex grep text-processing
What does [0-9] match?;[0-9] matches one digit from 0 through 9.;regex grep text-processing
What does grep -n do?;grep -n shows matching lines with their line numbers.;regex grep text-processing
What does grep -v do?;grep -v shows lines that do not match the pattern.;regex grep text-processing
Why use grep -E?;grep -E enables extended regular expressions, making operators like +, ?, {}, and | easier to use.;regex grep text-processing
What does + mean when used with grep -E?;+ means one or more repetitions of the previous character or pattern.;regex grep text-processing
What does ? mean when used with grep -E?;? means the previous character or pattern is optional.;regex grep text-processing
What does {3} mean when used with grep -E?;{3} means the previous character or pattern must repeat exactly three times.;regex grep text-processing
What does | mean when used with grep -E?;| means or, allowing either pattern to match.;regex grep text-processing
Why do you write \. when searching for a real period?;Because . normally means any single character, so \. escapes it and matches a literal period.;regex grep text-processing
