# Kernel Runtime Parameters

> Part of [[Operations & Deployment]] | [[00 - LFCS MOC]]

---

## What Are Kernel Runtime Parameters?

Settings that control how the Linux kernel behaves internally — things like memory management, network handling, and filesystem behavior. They can be changed **without rebooting** (non-persistent), or made **permanent** via config files.

Parameter naming convention:
- `net.*` — network settings
- `vm.*` — virtual memory / RAM behavior
- `fs.*` — filesystem settings
- `kernel.*` — core kernel behavior

---

## Viewing Parameters

```bash
sysctl -a                          # list all parameters and values
sudo sysctl -a                     # some params require root to read
sysctl net.ipv6.conf.default.disable_ipv6   # read a specific parameter
```

Filter by category:
```bash
sysctl -a | grep vm                # memory-related parameters
sysctl -a | grep net               # network-related parameters
sysctl -a | grep forward           # IP forwarding parameters
```

---

## Changing Parameters (Non-Persistent)

Changes made with `-w` take effect immediately but **do not survive a reboot**.

```bash
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
# No spaces around the = sign
```

Verify the change:
```bash
sysctl net.ipv6.conf.default.disable_ipv6
```

---

## Making Changes Persistent

Add a `.conf` file to `/etc/sysctl.d/`. The filename can be anything, but **must end in `.conf`**.

```bash
sudo vim /etc/sysctl.d/swap-less.conf
```

File contents (one parameter per line):
```
vm.swappiness=20
```

Apply immediately (without rebooting):
```bash
sudo sysctl -p /etc/sysctl.d/swap-less.conf
```

Apply **all** sysctl config files:
```bash
sudo sysctl --system
```

> Check the man page if you forget the `.conf` requirement: `man sysctl.d`

---

## Common Parameters

### vm.swappiness

Controls how aggressively Linux uses swap space.

- Range: 0–100 (older kernels), 0–200 (newer kernels)
- Higher value → swap more freely
- Lower value → avoid swapping, keep data in RAM

```bash
sysctl vm.swappiness                          # check current value (default: 60)
sudo sysctl -w vm.swappiness=20               # temporary change
```

Persistent:
```bash
sudo vim /etc/sysctl.d/swap-less.conf
# Add: vm.swappiness=20
sudo sysctl -p /etc/sysctl.d/swap-less.conf
```

### net.ipv4.ip_forward

Enables IP forwarding — required for port redirection and NAT.

```bash
# Enable temporarily
sudo sysctl -w net.ipv4.ip_forward=1

# Enable persistently
sudo vim /etc/sysctl.d/99-sysctl.conf
# Uncomment: net.ipv4.ip_forward=1
sudo sysctl --system
```

### net.ipv6.conf.all.forwarding

Same as above but for IPv6.

```bash
sudo sysctl -w net.ipv6.conf.all.forwarding=1
```

---

## Alternative: /etc/sysctl.conf

You can also edit `/etc/sysctl.conf` directly — it has helpful comments explaining common parameters.

```bash
sudo vim /etc/sysctl.conf
```

> **Warning:** This file can be overwritten during OS upgrades. Prefer `/etc/sysctl.d/` for your own changes.

---

## Exam Tips

> **Exam tip:** `-w` writes a **temporary** value. For persistence, the value must be in a `.conf` file in `/etc/sysctl.d/`.

> **Exam tip:** After creating a `.conf` file, run `sudo sysctl -p /etc/sysctl.d/yourfile.conf` to apply it immediately — otherwise you'd have to reboot for it to take effect.

> **Exam tip:** Don't forget the `.conf` extension on your file in `/etc/sysctl.d/`. It won't be loaded without it.

> **Exam tip:** `sudo sysctl --system` reloads *all* sysctl config files at once — useful after enabling IP forwarding.

---

## Related Notes

- [[Process Management]]
- [[Operations & Deployment]]
- [[Network Configuration]] — IP forwarding needed for port redirection/NAT
