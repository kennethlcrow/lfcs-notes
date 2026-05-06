# Compare and Manipulate File Content

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

## Context

This note covers the LFCS basics for comparing text files and making simple content changes based on what you find.

The main command in this lesson is `diff`.

Use these skills when you need to answer questions like:

- Did this file change?
- Which line is different?
- Was content added, removed, or edited?
- How can I verify a file edit?

## Key Concepts

### File Comparison

File comparison means checking two files to see whether their contents are the same or different.

Linux compares text files line by line. It does not understand the meaning of the text. It only checks whether the actual lines match.

Example:

    port=80

and:

    port=8080

These lines are different because the text is not exactly the same.

### The `diff` Command

`diff` compares two files and shows the lines that are different.

Command structure:

    diff [options] file1 file2

Breakdown:

- `diff` is the command.
- `[options]` changes how the output is displayed.
- `file1` is the first file being compared.
- `file2` is the second file being compared.

Basic example:

    diff old.txt new.txt

Think of the first file as the original version and the second file as the changed version.

## Understanding Basic `diff` Output

Example output:

    2c2
    < port=80
    ---
    > port=8080

Meaning:

- `2c2` means line 2 in the first file was changed to line 2 in the second file.
- `<` shows content from the first file.
- `>` shows content from the second file.
- `---` separates the two versions.

Common change letters:

- `c` means changed.
- `a` means added.
- `d` means deleted.

Important beginner rule:

- `<` points to the first file.
- `>` points to the second file.

## Contextual Differences

Basic `diff` output can feel too small when files are longer. Context output shows nearby lines so you can understand where the change happened.

Command structure:

    diff -c file1 file2

Breakdown:

- `diff` compares the files.
- `-c` shows context around differences.
- `file1` is the first file.
- `file2` is the second file.

Symbols in context output:

- `!` means a line changed.
- `+` means a line was added.
- `-` means a line was removed.

Example:

    diff -c old.conf new.conf

Use context output when you want a clearer view of changes inside a larger file.

## Side-by-Side Comparison

Side-by-side comparison shows both files next to each other.

Command structure:

    diff -y file1 file2

Breakdown:

- `diff` compares the files.
- `-y` displays the output side by side.
- `file1` appears on the left.
- `file2` appears on the right.

Common side-by-side symbols:

- `|` means the line is different.
- `<` means the line exists only in the first file.
- `>` means the line exists only in the second file.

Example:

    diff -y old.conf new.conf

This is useful when comparing short files visually.

## Manipulating File Content

After comparing files, you may need to create a corrected version.

Basic commands:

    cp source destination

Copies a file.

    mv oldname newname

Renames or moves a file.

    echo "text" > file

Creates or replaces a file with text.

    echo "text" >> file

Adds text to the end of a file.

    sed 's/old/new/' file

Shows file content with the first matching text replaced on each matching line.

Important beginner warning:

- `>` replaces file content.
- `>>` appends to file content.
- Make a copy before editing important files.

## Hands-On Lab 1: Compare Two Simple Files

Start from the parent directory where you want the lab folders created.

Create and enter the lab directory:

    mkdir lab1
    cd lab1

Create the first file:

    echo "server=web01" > old.conf
    echo "port=80" >> old.conf
    echo "status=enabled" >> old.conf

Create the second file:

    echo "server=web01" > new.conf
    echo "port=8080" >> new.conf
    echo "status=enabled" >> new.conf

View both files:

    cat old.conf
    cat new.conf

Compare them:

    diff old.conf new.conf

What to notice:

- The changed line from `old.conf` starts with `<`.
- The changed line from `new.conf` starts with `>`.

## Hands-On Lab 2: Use Context Output

Start from the same parent directory where `lab1` was created.

Create and enter the lab directory:

    mkdir lab2
    cd lab2

Create the first file:

    echo "Application Settings" > app-old.conf
    echo "name=myapp" >> app-old.conf
    echo "port=80" >> app-old.conf
    echo "mode=production" >> app-old.conf
    echo "logging=on" >> app-old.conf

Create the second file:

    echo "Application Settings" > app-new.conf
    echo "name=myapp" >> app-new.conf
    echo "port=8080" >> app-new.conf
    echo "mode=production" >> app-new.conf
    echo "logging=on" >> app-new.conf

Run context diff:

    diff -c app-old.conf app-new.conf

What to notice:

- `!` marks the changed line.
- Nearby unchanged lines help you understand where the change happened.

## Hands-On Lab 3: Side-by-Side Compare and Create a Corrected File

Start from the same parent directory where `lab1` and `lab2` were created.

Create and enter the lab directory:

    mkdir lab3
    cd lab3

Create the first file:

    echo "hostname=server1" > config-a.conf
    echo "ip=192.168.1.10" >> config-a.conf
    echo "backup=no" >> config-a.conf

Create the second file:

    echo "hostname=server1" > config-b.conf
    echo "ip=192.168.1.20" >> config-b.conf
    echo "backup=yes" >> config-b.conf

Compare side by side:

    diff -y config-a.conf config-b.conf

Create a corrected copy from the first file:

    cp config-a.conf corrected.conf

Show the copied file:

    cat corrected.conf

Replace the backup line and write the result to a new file:

    sed 's/backup=no/backup=yes/' corrected.conf > corrected-new.conf

View the corrected version:

    cat corrected-new.conf

Compare the original and corrected version:

    diff corrected.conf corrected-new.conf

What to notice:

- `cp` preserves the original before changes.
- `sed` can produce edited content.
- `diff` confirms what changed.

## Cleanup

Run these commands from the same parent directory where the lab folders were created.

If you are still inside `lab1`, `lab2`, or `lab3`, go back to the parent directory first:

    cd ..

Remove Lab 1:

    rm -r lab1

Remove Lab 2:

    rm -r lab2

Remove Lab 3:

    rm -r lab3

Nothing else was created.

## Recap Checklist

- `diff file1 file2` compares two files line by line.
- `<` shows lines from the first file.
- `>` shows lines from the second file.
- `diff -c file1 file2` shows differences with surrounding context.
- In context output, `!` means changed, `+` means added, and `-` means removed.
- `diff -y file1 file2` shows files side by side.
- `cp` lets you make a safe copy before editing.
- `>` replaces file content.
- `>>` appends to file content.
- Use `diff` after editing to confirm exactly what changed.

## Flashcards

What is the purpose of the diff command?;The diff command compares two text files line by line and shows differences.;file-comparison diff
What is the basic structure of the diff command?;diff [options] file1 file2;file-comparison diff
In diff output, what does < mean?;< shows a line from the first file.;file-comparison diff
In diff output, what does > mean?;> shows a line from the second file.;file-comparison diff
What does the c letter mean in basic diff output?;c means a line was changed between the two files.;file-comparison diff
What does the a letter mean in basic diff output?;a means content was added in the second file compared with the first.;file-comparison diff
What does the d letter mean in basic diff output?;d means content was deleted from the first file compared with the second.;file-comparison diff
What does diff -c do?;diff -c shows differences with surrounding context lines.;file-comparison diff
In context diff output, what does ! mean?;! marks a line that changed.;file-comparison diff
In context diff output, what does + mean?;+ marks a line that was added.;file-comparison diff
In context diff output, what does - mean?;- marks a line that was removed.;file-comparison diff
What does diff -y do?;diff -y displays file differences side by side.;file-comparison diff
In side-by-side diff output, what does | mean?;| means the lines on the left and right are different.;file-comparison diff
Why should you copy a file before editing it?;Copying creates a safe backup or working version before making changes.;file-comparison diff
What is the difference between > and >>?;> replaces file content, while >> appends to the end of the file.;file-comparison diff
