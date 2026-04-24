# Process Management

> Part of [[Operations & Deployment]] | [[00 - LFCS MOC]]

---

## What is a Process?

When a program runs, the OS creates a **process** — a running instance of that program loaded into memory. Every process gets a unique **PID** (Process IDentifier).

---

## Viewing Processes

```bash
ps aux                             # snapshot of all running processes
ps aux | grep nginx                # filter for a specific process
ps axZ                             # include SELinux security context

top                                # live, interactive process viewer
htop                               # improved top (may need to install)

uptime                             # system uptime + CPU load averages
```

### Reading `uptime` Load Averages

```
load average: 1.23, 0.45, 0.31
              ^1min  ^5min  ^15min
```

- Each number = average CPU cores used at 100% during that period
- `1.0` on a single-core system = 100% CPU utilization
- Compare to your core count (`lscpu`) — values consistently above core count = overloaded

```bash
lscpu                              # see number of CPU cores
lspci                              # list PCI devices
```

---

## Resource Monitoring

```bash
# Disk space
df -h                              # filesystem usage (human-readable)
du -sh /var/log/                   # size of a specific directory
du -sh /*                          # size of each top-level directory

# Memory
free -h                            # RAM + swap usage
                                   # "available" column = what programs can actually use

# Storage I/O
iostat                             # I/O statistics since boot
iostat -x 1                        # extended stats, refreshed every 1 second
pidstat -d 1                       # per-process I/O stats (requires sysstat)

sudo apt install sysstat           # install iostat + pidstat
```

### Filesystem Integrity Checks

> Must unmount the filesystem first before checking.

```bash
sudo xfs_repair -v /dev/vdb1       # check and repair XFS filesystem
sudo fsck.ext4 -v -f -p /dev/vdb2  # check and repair ext4 filesystem
```

`fsck.ext4` options:
- `-v` verbose output
- `-f` force check even if filesystem reports clean
- `-p` preen mode — auto-fix simple errors without asking

---

## Signals & Killing Processes

```bash
kill PID                           # send SIGTERM (graceful shutdown request)
kill -9 PID                        # send SIGKILL (force kill — no cleanup)
kill -SIGTERM PID                  # same as kill PID

killall nginx                      # kill all processes named nginx
pkill nginx                        # kill processes matching pattern

sudo pkill atd                     # example: kill the at daemon
```

**Common signals:**

| Signal | Number | Meaning |
|---|---|---|
| SIGTERM | 15 | Politely ask process to stop |
| SIGKILL | 9 | Force stop immediately (cannot be caught) |
| SIGHUP | 1 | Reload config (many daemons respond to this) |
| SIGSTOP | 19 | Pause process |
| SIGCONT | 18 | Resume paused process |

---

## Background & Foreground Jobs

```bash
command &                          # run command in background
jobs                               # list background jobs
fg %1                              # bring job #1 to foreground
bg %1                              # resume stopped job in background
Ctrl+Z                             # pause (suspend) foreground job
Ctrl+C                             # interrupt (kill) foreground job
```

---

## nice & renice — Process Priority

Priority ranges from **-20** (highest priority) to **19** (lowest priority). Default is 0.

```bash
nice -n 10 command                 # start command with lower priority (+10)
nice -n -5 command                 # start with higher priority (requires root)

renice -n 5 -p PID                 # change priority of running process
renice -n 5 -u username            # change priority for all processes of a user

sudo renice -n -5 -p PID           # only root can set negative (higher priority)
```

---

## Checking Services & Dependencies

```bash
systemctl list-dependencies        # tree of active/inactive systemd units
systemctl status atd.service       # check status of a specific service
journalctl -u atd.service          # logs for a specific service

sudo systemctl start atd.service   # restart a stopped service
```

> If a service shows as inactive in `systemctl list-dependencies`, check `systemctl status` for the reason. Fix the underlying issue first, then start the service.

---

## lsof — List Open Files

```bash
sudo lsof -p PID                   # files opened by a process
sudo lsof /var/log/syslog          # what process has this file open
lsof -i :22                        # what's listening on port 22
```

---

## Exam Tips

> **Exam tip:** `systemctl list-dependencies` is your quick health check. Green = active, white = inactive. Critical services (`ssh`, `cron`, `atd`) should always be green.

> **Exam tip:** When debugging a dead service: `systemctl status` → `journalctl -u` → fix the problem → `systemctl start`.

> **Exam tip:** Know the difference between `kill` (SIGTERM, graceful) and `kill -9` (SIGKILL, force). Always try SIGTERM first.

---

## Related Notes

- [[systemd]]
- [[Scheduling Tasks]]
- [[Kernel Runtime Parameters]]
- [[Operations & Deployment]]
