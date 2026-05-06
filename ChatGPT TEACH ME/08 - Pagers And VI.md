# Pagers And VI

**Parent:** [[00 - ChatGPT TEACH ME MOC]]

## Overview

This lesson covers the basics of using terminal pagers and the Vim text editor.

A pager lets you view text one screen at a time. A text editor lets you change text.

Think of a pager like reading a file behind glass: you can move around and search, but you do not edit the file. Vim is different: it lets you open a file, type into it, change it, save it, and quit.

Common pagers:

- `less`
- `more`

Common terminal editor:

- `vim`
- `vi`

`vim` means `VI improved`. On many Linux systems, typing `vi` may open Vim or a Vim-compatible editor.

## Key Concepts And Definitions

### Pager

A pager is a program that displays long text one page at a time in the terminal.

Pagers are useful when output is too long to fit on one screen.

Examples of things often viewed in a pager:

- manual pages
- log files
- configuration files
- long command output

### less

`less` is a flexible pager. It lets you move forward and backward, search text, and quit when finished.

Common keys inside `less`:

- `Space` moves forward one page
- `b` moves backward one page
- Arrow keys move line by line
- `/word` searches forward for `word`
- `n` jumps to the next search match
- `N` jumps to the previous search match
- `q` quits

### more

`more` is a simpler pager. It is mostly used for moving forward through text.

Common keys inside `more`:

- `Space` moves forward one page
- `Enter` moves forward one line
- `q` quits

### Vim

Vim is a terminal text editor.

Vim is mode-sensitive, which means the same key can do different things depending on which mode you are in.

The two most important beginner modes are:

- Normal mode: used for commands like saving, quitting, searching, copying, deleting, and moving
- Insert mode: used for typing text into the file

When Vim opens, it usually starts in Normal mode.

## Command Structure Breakdown

### Opening A File With less

    less filename

Breakdown:

- `less` is the command
- `filename` is the file you want to view

Example:

    less file.txt

### Opening A File With Case-Insensitive Search In less

    less -i filename

Breakdown:

- `less` opens the pager
- `-i` makes searches ignore case
- `filename` is the file you want to view

Example:

    less -i file.txt

### Sending Command Output Into less

    command | less

Breakdown:

- `command` produces output
- `|` sends that output into another command
- `less` displays the output one page at a time

Example:

    ls -l /etc | less

### Opening A File With more

    more filename

Breakdown:

- `more` is the command
- `filename` is the file you want to view

Example:

    more file.txt

### Opening A File With Vim

    vim filename

Breakdown:

- `vim` opens the Vim editor
- `filename` is the file you want to create or edit

Example:

    vim notes.txt

If `notes.txt` already exists, Vim opens it. If it does not exist, Vim creates it when you save.

## Clean Examples

### View A File With less

    less lesson.txt

Search inside `less`:

    /linux

Move to the next match:

    n

Quit:

    q

### View A File With more

    more lesson.txt

Move forward one page:

    Space

Move forward one line:

    Enter

Quit:

    q

### Basic Vim Save Workflow

Open a file:

    vim notes.txt

Enter Insert mode:

    i

Type your text.

Return to Normal mode:

    Esc

Save and quit:

    :wq

### Basic Vim Quit Commands

Save and quit:

    :wq

Quit without saving:

    :q!

Save but stay in Vim:

    :w

Quit if no changes were made:

    :q

### Searching In Vim

Search for a word:

    /word

Move to the next match:

    n

Move to the previous match:

    N

Search case-insensitively:

    /\cword

Example:

    /\clinux

### Basic Vim Text Manipulation

Copy the current line:

    yy

Cut or delete the current line:

    dd

Paste below the current line:

    p

## Hands-On Labs

## Lab 1: View A File With less

Start from the parent directory where your labs will be created.

1. Create and enter the lab directory:

    mkdir lab1
    cd lab1

2. Create a sample file:

    printf "Linux basics\nPagers help you read text\nVim helps you edit text\nLinux commands are case-sensitive\n" > lesson.txt

3. Open the file with `less`:

    less lesson.txt

4. Inside `less`, search for `Vim`:

    /Vim

5. Press:

    Enter

6. Quit `less`:

    q

7. Open the file again with case-insensitive searching:

    less -i lesson.txt

8. Inside `less`, search using lowercase:

    /linux

9. Press:

    Enter

10. Move to the next match:

    n

11. Quit:

    q

What you practiced:

- opening a file with `less`
- searching inside `less`
- quitting `less`
- using case-insensitive search

## Lab 2: View A File With more

Start from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

1. Create and enter the lab directory:

    mkdir lab2
    cd lab2

2. Create a longer sample file:

    printf "Line 1\nLine 2\nLine 3\nLine 4\nLine 5\nLine 6\nLine 7\nLine 8\nLine 9\nLine 10\n" > lines.txt

3. Open the file with `more`:

    more lines.txt

4. Press `Space` to move forward.

5. Press `Enter` to move one line at a time.

6. Press `q` to quit.

What you practiced:

- opening a file with `more`
- moving through text
- quitting `more`

## Lab 3: Create And Edit A File With Vim

Start from the same parent directory where `lab1`, `lab2`, and `lab3` will be created.

1. Create and enter the lab directory:

    mkdir lab3
    cd lab3

2. Open a new file with Vim:

    vim notes.txt

3. Press `i` to enter Insert mode.

4. Type these lines:

    Linux uses text files for many settings.
    Pagers help me read files.
    Vim helps me edit files.

5. Press `Esc` to return to Normal mode.

6. Save and quit:

    :wq

7. Confirm the file exists and show its contents:

    cat notes.txt

8. Open the file again:

    vim notes.txt

9. Search for `vim` case-insensitively:

    /\cvim

10. Press `Enter`.

11. Press `Esc`.

12. Quit without making changes:

    :q!

What you practiced:

- opening Vim
- entering Insert mode
- saving and quitting
- viewing the saved file
- searching in Vim
- quitting without saving

## Cleanup

Assume you are running cleanup from the same parent directory where the labs were created.

1. If you are still inside `lab1`, `lab2`, or `lab3`, return to the parent directory first:

    cd ..

2. Remove Lab 1:

    rm -r lab1

3. Remove Lab 2:

    rm -r lab2

4. Remove Lab 3:

    rm -r lab3

No users were created.

No groups were created.

No services were started.

No system files were changed.

## Notes From Follow-Up Questions Or Clarifications

No follow-up clarifications were added yet.

## Recap Checklist

- A pager lets you read text one screen at a time.
- `less file.txt` opens a file in the `less` pager.
- `more file.txt` opens a file in the simpler `more` pager.
- In `less`, use `/word` to search.
- In `less`, use `q` to quit.
- `less -i file.txt` helps with case-insensitive searching.
- Vim is a mode-sensitive text editor.
- Press `i` in Vim to enter Insert mode.
- Press `Esc` to return to Normal mode.
- Use `:wq` to save and quit.
- Use `:q!` to quit without saving.
- In Vim, use `/word` to search.
- In Vim, `yy` copies a line.
- In Vim, `dd` cuts or deletes a line.
- In Vim, `p` pastes.

## Flashcards
What is a pager in Linux?;A pager is a program that displays long text one screen at a time.;pagers vim text-editing
What are two common Linux pagers?;Two common pagers are less and more.;pagers vim text-editing
What is the basic command structure for opening a file with less?;Use less filename, where less is the command and filename is the file to view.;pagers vim text-editing
How do you quit less?;Press q to quit less.;pagers vim text-editing
How do you search for a word inside less?;Type /word and press Enter.;pagers vim text-editing
What does less -i filename do?;It opens a file in less and makes searches ignore case.;pagers vim text-editing
What is the main difference between less and more?;less is more flexible and can move backward easily, while more is simpler and mostly moves forward.;pagers vim text-editing
What does Vim stand for?;Vim stands for VI improved.;pagers vim text-editing
Why is Vim called mode-sensitive?;Vim is mode-sensitive because keys behave differently depending on the current mode.;pagers vim text-editing
What are the two most important beginner Vim modes?;Normal mode and Insert mode.;pagers vim text-editing
How do you enter Insert mode in Vim?;Press i.;pagers vim text-editing
How do you return to Normal mode in Vim?;Press Esc.;pagers vim text-editing
What Vim command saves and quits?;:wq saves and quits.;pagers vim text-editing
What Vim command quits without saving changes?;:q! quits without saving changes.;pagers vim text-editing
How do you search for a word in Vim?;In Normal mode, type /word and press Enter.;pagers vim text-editing
How do you search case-insensitively in Vim for one search?;Use /\cword, replacing word with the search term.;pagers vim text-editing
What does yy do in Vim Normal mode?;yy copies the current line.;pagers vim text-editing
What does dd do in Vim Normal mode?;dd cuts or deletes the current line.;pagers vim text-editing
What does p do in Vim Normal mode?;p pastes below the current line.;pagers vim text-editing
