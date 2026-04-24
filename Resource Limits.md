# Resource Limits

> Part of [[Users & Groups]] | [[00 - LFCS MOC]]

---

## What Are Resource Limits?

Linux can cap how much CPU, memory, file handles, processes, and other resources any user or process can consume. This prevents a single user or runaway process from starving the system.

Two layers:
- **ulimit** — per-shell/process limits for the current session
- **pam_limits** — limits applied at login via PAM (persistent)

---

## ulimit — Shell Limits

```bash
ulimit -a                            # show all limits for current shell
ulimit -n                            # show open file limit
ulimit -u                            # show max user processes
```

### Setting Limits (Current Session Only)

```bash
ulimit -n 4096                       # max open files: 4096
ulimit -u 200                        # max processes: 200
ulimit -v 1048576                    # max virtual memory: 1GB (in KB)
ulimit -c unlimited                  # allow unlimited core dumps
ulimit -f 102400                     # max file size: 100MB (in blocks)
```

> `ulimit` changes only affect the current shell and its children. Not persistent.

---

## /etc/security/limits.conf — Persistent Limits

Applied to users at login via PAM. Persists across reboots.

```bash
sudo vim /etc/security/limits.conf
man limits.conf
```

### Syntax

```
<domain>   <type>   <item>   <value>
```

| Field | Examples |
|---|---|
| domain | `jane`, `@developers`, `*` (all users) |
| type | `soft` (warning limit), `hard` (enforced max) |
| item | `nofile`, `nproc`, `memlock`, `fsize`, etc. |
| value | Integer, or `unlimited` |

### Common Limit Items

| Item | Description |
|---|---|
| `nofile` | Max open file descriptors |
| `nproc` | Max number of processes |
| `fsize` | Max file size (KB) |
| `memlock` | Max locked memory (KB) |
| `core` | Max core dump size (KB) |
| `cpu` | Max CPU time (minutes) |
| `stack` | Max stack size (KB) |
| `as` | Max address space / virtual memory (KB) |

### Example Entries

```
# Limit jane to 100 processes
jane    soft    nproc   100
jane    hard    nproc   150

# Allow database group more open files
@dbgroup  soft  nofile   65536
@dbgroup  hard  nofile   65536

# Restrict all users
*       soft    nproc   256
*       hard    nproc   512
*       soft    nofile  1024
*       hard    nofile  4096
```

**soft vs hard:**
- `soft` — default limit; user can raise up to the hard limit
- `hard` — absolute ceiling; only root can raise it

---

## /etc/security/limits.d/

Drop-in directory — cleaner than editing `limits.conf` directly.

```bash
sudo vim /etc/security/limits.d/99-custom.conf
```

---

## PAM — Enabling Limits

`limits.conf` requires `pam_limits.so` to be loaded. Verify it's active:

```bash
grep pam_limits /etc/pam.d/common-session
# Should show: session required pam_limits.so
```

If missing:
```bash
sudo vim /etc/pam.d/common-session
# Add: session required pam_limits.so
```

---

## systemd Limits (Services)

For system services (not user sessions), limits are set in the unit file or its override:

```bash
sudo systemctl edit nginx.service
```

```ini
[Service]
LimitNOFILE=65536
LimitNPROC=512
```

```bash
sudo systemctl daemon-reload
sudo systemctl restart nginx.service
```

---

## cgroups — Control Groups

cgroups provide more fine-grained resource control at the kernel level. systemd uses them internally.

```bash
systemd-cgtop                        # live view of resource usage by cgroup
systemctl status nginx.service       # shows cgroup for the service
```

---

## Checking Applied Limits

```bash
# For a running process (PID):
cat /proc/PID/limits

# For current session:
ulimit -a

# Verify login limits are applied (login as user and check):
su - jane
ulimit -a
```

---

## Exam Tips

> **Exam tip:** `ulimit` changes are temporary (current shell only). For persistent limits, use `/etc/security/limits.conf`.

> **Exam tip:** Limits in `limits.conf` only apply at **login**. For them to take effect for a user, they must log out and back in.

> **Exam tip:** `soft` limits are the starting value; users can raise them up to the `hard` limit. Only root can raise the hard limit.

> **Exam tip:** If limits from `limits.conf` don't seem to apply, check that `pam_limits.so` is in `/etc/pam.d/common-session`.

---

## Related Notes

- [[Local Users & Groups]] — managing users the limits apply to
- [[systemd]] — service-level limits via unit files
- [[Users & Groups]]
