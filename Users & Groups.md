# Users & Groups

> Exam weight: **10%** | Part of [[00 - LFCS MOC]]

---

## Domain Overview

Users & Groups is the smallest exam domain but important for access control, security, and resource management.

### Topics

- [[Local Users & Groups]] — creating and managing users and groups
- [[Environment Profiles]] — shell startup files, PATH, environment variables
- [[Resource Limits]] — ulimit, pam_limits, controlling resource usage per user
- [[LDAP Authentication]] — centralized authentication with LDAP/SSSD

---

## Key Files

| File | Purpose |
|---|---|
| `/etc/passwd` | User accounts (username, UID, home dir, shell) |
| `/etc/shadow` | Password hashes (root-only) |
| `/etc/group` | Group definitions |
| `/etc/gshadow` | Group passwords (rarely used) |
| `/etc/sudoers` | Sudo privileges |
| `/etc/skel/` | Template for new user home directories |

---

## Quick Reference

```bash
id                                 # show current user's UID, GID, groups
whoami                             # current username
groups                             # groups current user belongs to
id username                        # info for a specific user

# Users
sudo useradd -m -s /bin/bash jane  # create user with home dir and bash shell
sudo passwd jane                   # set password
sudo usermod -aG sudo jane         # add to sudo group
sudo userdel -r jane               # delete user and home directory

# Groups
sudo groupadd developers           # create group
sudo gpasswd -a jane developers    # add user to group
sudo gpasswd -d jane developers    # remove user from group
sudo groupdel developers           # delete group
```

---

## Exam Tips

> **Exam tip:** `usermod -aG` (append to Group) — the `-a` flag is critical. Without it, the user is **removed** from all other groups.

> **Exam tip:** Group membership changes take effect on the user's **next login**. In exam labs, `su - username` to test as the new user.

> **Exam tip:** Resource limits set in `/etc/security/limits.conf` require PAM (`pam_limits.so`) to be loaded — check `/etc/pam.d/common-session`.

---

## Related Notes

- [[Local Users & Groups]]
- [[Environment Profiles]]
- [[Resource Limits]]
- [[LDAP Authentication]]
