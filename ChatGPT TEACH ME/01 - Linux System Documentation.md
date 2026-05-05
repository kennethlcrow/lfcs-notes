# Reading and Using System Documentation (Linux)

## Overview

In Linux, you are not expected to memorize every command or option. Instead, you are expected to know how to quickly **find and understand documentation** when you need it.

This is a critical skill for both real-world system administration and the LFCS exam.

> Key idea: *You don’t memorize Linux — you learn how to look things up efficiently.*

---

## Command Structure

Most Linux commands follow this format:

    command [options] [arguments]

Example:

    ls -l /home

- `ls` → command  
- `-l` → option (modifies behavior)  
- `/home` → argument (target)

Documentation tools explain:
- What options exist
- What arguments are expected
- What the command does

---

## Using --help (Quick Reference)

Use `--help` when you need a fast overview of a command.

Example:

    ls --help

What it provides:
- List of options
- Short descriptions
- Basic usage

Use this when:
- You already know the command
- You need a quick reminder

---

## Using man Pages (Full Documentation)

Use `man` for detailed documentation.

Example:

    man ls

### Common Sections

- NAME → command name and purpose  
- SYNOPSIS → how to use the command  
- DESCRIPTION → detailed explanation  
- OPTIONS → list of available flags  

### Navigation

Inside a man page:

- `Space` → move forward  
- `b` → move backward  
- `/word` → search  
- `n` → next match  
- `q` → quit  

---

## Man Page Sections

Sometimes commands exist in multiple sections.

Example:

    man 1 passwd
    man 5 passwd

- Section 1 → user commands  
- Section 5 → configuration files  
- Section 8 → administrative commands  

If something looks confusing, you may be in the wrong section.

---

## Using apropos (Search Tool)

If you don’t remember a command name, use `apropos`.

Example:

    apropos password

This searches descriptions in man pages and returns matching commands.

You can then open the correct manual:

    man passwd

You can limit results:

    apropos -s 1,8 password

---

## TAB Autocompletion

TAB helps you complete commands and file paths.

Examples:

    ls /u<TAB>        → /usr/
    systemc<TAB>     → systemctl

Double TAB shows options:

    systemctl<TAB><TAB>

Benefits:
- Saves time
- Prevents typos
- Helps discover commands

---

## Real-World Workflow

Typical flow when working in Linux:

1. Try the command
2. If unsure → use `--help`
3. If still unsure → use `man`
4. If you forgot the command → use `apropos`
5. Use TAB to speed everything up

---

## Hands-On Labs

### Lab 1 – Using --help

    mkdir lab1
    cd lab1

    ls --help
    mkdir --help
    cp --help

    mkdir testdir
    ls -l

---

### Lab 2 – Using man Pages

    cd ..
    mkdir lab2
    cd lab2

    man ls

    # Inside man:
    # /l   → search
    # n    → next match
    # q    → quit

    man mkdir

---

### Lab 3 – Using apropos

    cd ..
    mkdir lab3
    cd lab3

    apropos directory
    man mkdir

    apropos copy
    man cp

---

## Cleanup

    cd ..
    rm -r lab1
    rm -r lab2
    rm -r lab3

---

## Recap Checklist

- Understand command structure: command [options] [arguments]
- Use `--help` for quick references
- Use `man` for full documentation
- Navigate man pages effectively
- Use `apropos` to find unknown commands
- Use TAB for autocompletion

---

## Notes

- `man` and `apropos` are allowed during the LFCS exam — use them often :contentReference[oaicite:0]{index=0}  
- If `apropos` returns nothing, the database may need rebuilding:

    sudo mandb

- Always check the correct man section if results seem wrong

---

## Flashcards

What is the general structure of a Linux command?;command [options] [arguments];documentation  
What does the --help option provide?;A quick summary of command options and usage;documentation  
When should you use man instead of --help?;When you need detailed documentation;documentation  
How do you open the manual page for a command?;man command;documentation  
What key exits a man page?;q;documentation  
How do you search inside a man page?;/word;documentation  
What does apropos do?;Searches man page descriptions for keywords;documentation  
What should you do if you forget a command name?;Use apropos to search for it;documentation  
What does TAB do in the terminal?;Autocompletes commands and file paths;documentation  
What does pressing TAB twice do?;Shows all possible completions;documentation  
Why are man page sections important?;They separate commands, configs, and admin tools;documentation  
What is section 1 in man pages?;User commands;documentation  
What is section 8 in man pages?;Administrative commands;documentation  
What command rebuilds the apropos database?;sudo mandb;documentation  
What is the best workflow for learning Linux commands?;Use help tools instead of memorizing everything;documentation  

---

**MOC:** [[00 - ChatGPT TEACH ME MOC]]