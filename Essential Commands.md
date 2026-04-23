# Essential Commands

> Exam weight: **20%** | Part of [[00 - LFCS MOC]]

---

## Logging In

Four ways to log in:
- Local text-mode console
- Local graphical-mode console
- Remote text-mode (SSH) — most common for sysadmin work
- Remote graphical-mode

```bash
ssh username@IP_address        # connect to remote system
```

> A **terminal emulator** is a graphical app that behaves like a physical terminal — accepts commands, shows output. Consoles/terminals are software on modern Linux, not hardware.

---

## The Filesystem Tree

- Linux organizes files in an inverted tree — root `/` is at the top
- Everything lives under `/`
- Common top-level directories: `/home`, `/var`, `/etc`, `/tmp`, `/usr`

### Paths

| Type | Example | Notes |
|---|---|---|
| Absolute | `/home/aaron/Documents/file.pdf` | Always starts with `/` |
| Relative | `Documents/file.pdf` | Relative to current directory |

```bash
pwd                  # Print Working Directory — shows where you are
cd /var/log          # change to absolute path
cd Documents/        # change using relative path
cd ..                # go one directory UP (parent)
cd -                 # return to previous directory
cd                   # return to home directory
```

---

## File & Directory Operations

```bash
ls                   # list files in current directory
ls -a                # show hidden files (names starting with .)
ls -l                # long format — permissions, owner, size, date
ls -al               # combined — long format + hidden files
ls -alh              # human-readable file sizes (KB, MB, GB)

touch file.txt       # create empty file (or update timestamp)
mkdir Receipts/      # create directory

cp file.txt Receipts/              # copy file into directory
cp file.txt Receipts/copy.txt      # copy and rename
cp -r Receipts/ BackupReceipts/    # copy directory recursively

mv file.txt Receipts/              # move file
mv file.txt newname.txt            # rename file

rm file.txt          # delete file
rm -r Directory/     # delete directory and all contents
```

> **Habit:** End directory paths with `/` (e.g. `Receipts/`) — visual reminder it's a directory, not a file.

---

## Getting Help

```bash
ls --help            # quick reference, condensed
man ls               # full manual page (opens in pager)
man 1 ls             # section 1 = user commands
man 8 useradd        # section 8 = admin commands

# Navigate man pages:
# Space / f = forward  |  b = back  |  q = quit  |  /word = search

apropos director           # search man page descriptions
apropos -s 1,8 director    # limit to sections 1 (commands) and 8 (admin)
sudo mandb                 # rebuild apropos database if needed
```

> **Exam tip:** `man` and `apropos` are allowed during the exam. Get fast with them — they're your lifeline for forgotten syntax.

### TAB Autocompletion

```bash
systemc<TAB>         # completes to systemctl
systemctl<TAB><TAB>  # shows all available subcommands
ls /u<TAB>           # completes to /usr/
```

---

## Viewing & Working with Text Files

```bash
cat file.txt         # print entire file
tac file.txt         # print file in reverse (last line first)
head file.txt        # first 10 lines
head -n 5 file.txt   # first 5 lines
tail file.txt        # last 10 lines
tail -n 5 file.txt   # last 5 lines
tail -f file.txt     # follow — live updates (useful for logs)

less file.txt        # open in pager (navigate with space/b/q)
```

### Searching Text with grep

```bash
grep 'word' file.txt            # search for pattern in file
grep -r 'word' /etc/            # search recursively in directory
grep -i 'word' file.txt         # case-insensitive
grep -v 'word' file.txt         # invert — show lines NOT matching
grep -n 'word' file.txt         # show line numbers

egrep 'pattern' file.txt        # extended regex (supports |, +, ?)
egrep -r '/dev/[a-z]+' /etc/    # regex example
```

### sed — Stream Editor

```bash
sed 's/old/new/' file.txt        # replace first occurrence per line
sed 's/old/new/g' file.txt       # replace all occurrences
sed -i 's/old/new/g' file.txt    # edit file in place
```

### Sorting & Comparing

```bash
sort file.txt               # sort alphabetically
sort -r file.txt            # sort in reverse
sort -n numbers.txt         # sort numerically
uniq file.txt               # remove consecutive duplicate lines
sort file.txt | uniq        # sort then deduplicate

diff file1.txt file2.txt    # compare two files line by line
```

### cut & wc

```bash
cut -d ':' -f 1 /etc/passwd     # cut field 1, delimiter ':'
wc file.txt                     # word count (lines, words, chars)
wc -l file.txt                  # count lines only
```

---

## Finding Files

```bash
find /bin/ -name file1.txt            # find by exact name (case-sensitive)
find /bin/ -iname file1.txt           # case-insensitive name search
find / -name "f*"                     # wildcard — files starting with f
find / -type f                        # files only
find / -type d                        # directories only
find / -size +10M                     # larger than 10 megabytes
find / -mmin -1                       # modified in last 1 minute
find / -perm 644                      # exact permissions
find / -perm -664                     # at minimum these permissions
find / -perm /664                     # any of these permissions match
find / \! -perm -o=r                  # NOT readable by others
```

> **Remember:** `find LOCATION PARAMETERS` — location always comes first. "First go there, then find it."

---

## File Permissions

### Reading `ls -l` Output

```
-rwxr-xr--  1  aaron  family  4096  Jan 1  file.txt
│└──┬───┘      └──┬─┘ └──┬──┘
│   │             │       └── group owner
│   │             └────────── user owner
│   └──────────────────────── permissions (user | group | others)
└──────────────────────────── type: - file, d dir, l symlink
```

### Permission Bits

| Symbol | File | Directory |
|---|---|---|
| `r` | read contents | list contents |
| `w` | modify contents | create/delete files inside |
| `x` | execute file | enter directory (`cd` into it) |
| `-` | permission denied | permission denied |

### chmod — Change Permissions

```bash
# Symbolic
chmod u+x file.txt          # add execute for user
chmod g-w file.txt          # remove write for group
chmod o=r file.txt          # set others to read-only
chmod a+x file.txt          # add execute for all (a = all)

# Numeric (octal)
chmod 644 file.txt          # rw-r--r--
chmod 755 file.txt          # rwxr-xr-x
chmod 700 file.txt          # rwx------

# Octal reference:  r=4  w=2  x=1
# 7 = rwx | 6 = rw- | 5 = r-x | 4 = r-- | 0 = ---
```

### chown & chgrp

```bash
chgrp family file.txt               # change group
sudo chown jane file.txt            # change user owner (root only)
sudo chown aaron:family file.txt    # change user and group together
groups                              # see groups your user belongs to
```

---

## Hard Links & Soft Links

See also: [[Permissions & ACLs]]

### Hard Links

```bash
ln file.txt hardlink.txt            # create hard link
```

- Points directly to the same **inode** (actual data on disk)
- Both names are equal — deleting one doesn't affect the other
- Must be on the **same filesystem**
- Changing permissions on one changes all hard links (same inode)

### Soft Links (Symbolic Links)

```bash
ln -s Pictures/family_dog.jpg shortcut.jpg    # create soft link
ln -s Pictures/ shortcut_to_dir              # soft link to directory
readlink shortcut.jpg                         # show what link points to
```

- Points to a **path**, not an inode — like a Windows shortcut
- Can link across filesystems
- Broken if target path is renamed/deleted (shows red in `ls -l`)
- Permissions on the link itself don't matter — target permissions apply

---

## Archiving & Compression

### tar — Archive Files

```bash
tar cf archive.tar file1              # create archive
tar rf archive.tar file2              # append file to archive
tar tf archive.tar                    # list contents
tar xf archive.tar                    # extract archive
tar xf archive.tar -C /tmp/           # extract to specific directory
```

> Always run `tar tf archive.tar` before extracting — check where files will land.

### tar + Compression (one step)

```bash
tar czf archive.tar.gz file1          # gzip compression
tar cjf archive.tar.bz2 file1         # bzip2 compression
tar cJf archive.tar.xz file1          # xz compression
tar caf archive.tar.gz file1          # auto-detect from extension

tar xf archive.tar.gz                 # extract any compressed archive
```

### Compression Utilities

```bash
gzip file1                  # compress → file1.gz (original deleted)
gzip --keep file1           # compress and keep original
gzip --decompress file1.gz  # decompress
gunzip file1.gz             # decompress (shorthand)

bzip2 file1                 # compress → file1.bz2
bunzip2 file1.bz2

xz file1                    # compress → file1.xz
unxz file1.xz

zip archive.zip file1               # zip single file
zip -r archive.zip Pictures/        # zip entire directory
unzip archive.zip                   # extract zip
```

> `gzip`/`bzip2`/`xz` compress one file only — use with `tar` for directories. `zip` handles directories natively.

---

## Backup with rsync

```bash
rsync -a /local/dir/ user@IP:/remote/dir/    # sync to remote server
```

- `-a` = archive mode — preserves permissions, timestamps, subdirs
- Requires SSH daemon on remote server
- Only copies changed files (efficient for ongoing backups)

---

## Related Notes

- [[Permissions & ACLs]]
- [[File Operations]]
- [[Text Processing]]
- [[Archiving & Compression]]
- [[SSH]]
