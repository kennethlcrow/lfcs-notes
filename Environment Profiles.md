# Environment Profiles

> Part of [[Users & Groups]] | [[00 - LFCS MOC]]

---

## What Are Environment Profiles?

Shell startup files that set environment variables, aliases, functions, and PATH for users when they log in or open a terminal. Understanding which files run when — and for whom — is essential for system-wide vs. per-user configuration.

---

## Startup File Load Order

### Login Shell (e.g., SSH, `su -`)

System-wide first, then user-specific:

```
/etc/environment          → system-wide env variables (not a script)
/etc/profile              → system-wide shell config (bash)
/etc/profile.d/*.sh       → drop-in scripts for system-wide config
~/.bash_profile           → user-specific (runs instead of .bashrc for login shells)
~/.bash_login             → fallback if .bash_profile doesn't exist
~/.profile                → fallback if neither above exists
```

### Interactive Non-Login Shell (e.g., new terminal in GUI)

```
/etc/bash.bashrc          → system-wide interactive config
~/.bashrc                 → user-specific interactive config
```

### Logout

```
~/.bash_logout
```

---

## Key Files

### /etc/environment

Simple key=value pairs — no scripting, no `export`. Sets variables for all processes.

```bash
cat /etc/environment
sudo vim /etc/environment
```

```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
EDITOR="vim"
LANG="en_US.UTF-8"
```

### /etc/profile

Runs for all users on login. Good for system-wide settings.

```bash
cat /etc/profile
```

### /etc/profile.d/

Drop scripts here for system-wide config — cleaner than editing `/etc/profile` directly.

```bash
ls /etc/profile.d/
sudo vim /etc/profile.d/myapp.sh
```

```bash
#!/bin/bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
```

### ~/.bashrc

Per-user interactive shell config. Most commonly edited file for user customizations.

```bash
vim ~/.bashrc
source ~/.bashrc           # reload without logging out
```

### ~/.bash_profile (or ~/.profile)

Per-user login shell config. Often sources `~/.bashrc`:

```bash
# Typical ~/.bash_profile
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
```

---

## Environment Variables

```bash
env                                  # show all environment variables
printenv PATH                        # print a specific variable
echo $HOME                           # expand a variable

export MYVAR="hello"                 # set and export to child processes
unset MYVAR                          # remove a variable

# Temporary (current session only):
export PATH=$PATH:/opt/myapp/bin

# Permanent (add to ~/.bashrc or /etc/environment):
echo 'export PATH=$PATH:/opt/myapp/bin' >> ~/.bashrc
```

### Important Variables

| Variable | Purpose |
|---|---|
| `PATH` | Directories searched for commands |
| `HOME` | Current user's home directory |
| `USER` | Current username |
| `SHELL` | Current shell path |
| `EDITOR` | Default text editor |
| `LANG` | System locale |
| `PS1` | Shell prompt format |

---

## Aliases

```bash
alias ll='ls -alh'                   # define alias (current session)
alias grep='grep --color=auto'

# Permanent — add to ~/.bashrc:
echo "alias ll='ls -alh'" >> ~/.bashrc
source ~/.bashrc
```

---

## Modifying System-Wide PATH

```bash
# Best practice: add a file in /etc/profile.d/
sudo vim /etc/profile.d/custom-path.sh
```

```bash
export PATH=$PATH:/opt/myapp/bin
```

---

## Changing a User's Default Shell

```bash
sudo chsh -s /bin/zsh jane           # change jane's login shell
chsh -s /bin/bash                    # change your own shell

# Available shells
cat /etc/shells
```

---

## Exam Tips

> **Exam tip:** System-wide changes belong in `/etc/environment` (variables) or `/etc/profile.d/` (scripts). Per-user changes go in `~/.bashrc` or `~/.profile`.

> **Exam tip:** Changes to `~/.bashrc` require `source ~/.bashrc` to take effect in the current session. A new shell picks them up automatically.

> **Exam tip:** `/etc/environment` does NOT support scripting or `export` — it's just `KEY=value` pairs. Use `/etc/profile.d/` scripts when you need `export` or logic.

---

## Related Notes

- [[Local Users & Groups]] — which user runs which shell
- [[Resource Limits]] — limits applied at login
- [[Users & Groups]]
