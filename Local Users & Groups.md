# Local Users & Groups

> Part of [[Users & Groups]] | [[00 - LFCS MOC]]

---

## User Management

### Creating Users

```bash
sudo useradd jane                          # create user (no home dir by default on some distros)
sudo useradd -m jane                       # create with home directory
sudo useradd -m -s /bin/bash jane          # with bash as login shell
sudo useradd -m -s /bin/bash -G sudo jane  # add to sudo group at creation
sudo useradd -u 1500 jane                  # specify UID

sudo adduser jane                          # interactive wizard (Debian/Ubuntu)
```

### Setting & Changing Passwords

```bash
sudo passwd jane                           # set jane's password (as root)
passwd                                     # change your own password

sudo passwd -l jane                        # lock account (disable login)
sudo passwd -u jane                        # unlock account
sudo passwd -e jane                        # force password change on next login
```

### Modifying Users

```bash
sudo usermod -s /bin/zsh jane              # change login shell
sudo usermod -d /new/home jane             # change home directory
sudo usermod -l newname jane               # rename user

sudo usermod -aG developers jane           # add to group (keep existing groups!)
sudo usermod -G developers jane            # set groups (replaces all existing groups!)

sudo usermod -L jane                       # lock account
sudo usermod -U jane                       # unlock account
```

> **Critical:** Always use `-aG` (append + Group) to add a user to a group. Using `-G` alone replaces all group memberships.

### Deleting Users

```bash
sudo userdel jane                          # delete user (keeps home directory)
sudo userdel -r jane                       # delete user AND home directory
```

---

## /etc/passwd Format

```
username:x:UID:GID:comment:home_dir:shell
aaron:x:1000:1000:Aaron Lockhart:/home/aaron:/bin/bash
```

- `x` in password field = password hash in `/etc/shadow`
- UID 0 = root
- System accounts typically have UID < 1000

---

## /etc/shadow Format

```
username:$hash:last_change:min:max:warn:inactive:expire:
aaron:$6$hash...:19000:0:99999:7:::
```

---

## Group Management

```bash
sudo groupadd developers                   # create group
sudo groupadd -g 1500 developers           # with specific GID

sudo groupmod -n devs developers           # rename group
sudo groupdel developers                   # delete group

# Add/remove users from a group
sudo gpasswd -a jane developers            # add jane to group
sudo gpasswd -d jane developers            # remove jane from group
sudo gpasswd -M jane,aaron developers      # set group members (replaces all)

# Set group administrator
sudo gpasswd -A jane developers            # jane can manage the group
```

### /etc/group Format

```
group_name:x:GID:member1,member2
developers:x:1001:jane,aaron
```

---

## Checking User & Group Info

```bash
id                                         # current user's UID, GID, and all groups
id jane                                    # specific user
groups                                     # groups current user belongs to
groups jane                                # groups a specific user belongs to
whoami                                     # current username

getent passwd jane                         # lookup user in passwd database
getent group developers                    # lookup group

cat /etc/passwd                            # all users
cat /etc/group                             # all groups
```

---

## sudo — Running Commands as Root

```bash
sudo command                               # run as root
sudo -u jane command                       # run as specific user
sudo -i                                    # interactive root shell
sudo -l                                    # list your sudo privileges
```

### /etc/sudoers

```bash
sudo visudo                                # edit sudoers safely (validates syntax)
```

```
# Format:
# user  host=(runas)  commands
aaron   ALL=(ALL)   ALL                    # full sudo access
jane    ALL=(ALL)   NOPASSWD: ALL          # no password required
jane    ALL=(ALL)   /usr/bin/systemctl     # only specific commands
```

Grant sudo by adding to the `sudo` group:
```bash
sudo usermod -aG sudo jane                 # Debian/Ubuntu
sudo usermod -aG wheel jane               # RHEL/CentOS
```

---

## Switching Users

```bash
su jane                                    # switch to jane (uses current env)
su - jane                                  # switch with full login environment
su -                                       # switch to root
sudo su -                                  # become root via sudo
```

---

## Password Aging

```bash
sudo chage -l jane                         # list password aging info
sudo chage -M 90 jane                      # max password age: 90 days
sudo chage -m 7 jane                       # min password age: 7 days
sudo chage -W 14 jane                      # warn 14 days before expiry
sudo chage -E 2025-12-31 jane              # account expires on date
sudo chage -E -1 jane                      # never expire
```

---

## /etc/skel

New user home directories are populated from `/etc/skel/`. Any files placed here will appear in every new user's home.

```bash
ls -la /etc/skel/
sudo cp myconfig /etc/skel/.myconfig       # add file to all future users
```

---

## Exam Tips

> **Exam tip:** `useradd` vs `adduser` — `adduser` is a friendlier interactive wrapper; `useradd` is the base command. Know both.

> **Exam tip:** After adding a user to a group, the change takes effect on next login. Use `su - username` to test in labs.

> **Exam tip:** `visudo` validates syntax before saving — always use it instead of editing `/etc/sudoers` directly.

---

## Related Notes

- [[Environment Profiles]] — shell config per user
- [[Resource Limits]] — restrict what users can consume
- [[Permissions & ACLs]] — file-level access control
- [[Users & Groups]]
