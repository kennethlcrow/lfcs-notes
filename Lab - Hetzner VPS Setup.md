# Lab - Hetzner VPS Setup

## Overview

A persistent, remotely accessible Linux lab environment hosted on a Hetzner VPS. Designed to work from both home (Termius) and restricted work networks (Google Cloud Shell via browser).

| Host | IP | User |
|---|---|---|
| ubuntu1 | 23.88.36.107 | root |

---

## VPS Setup (Hetzner)

### Key choices

| Choice | Reason |
|---|---|
| Ubuntu 24.04 | LFCS-friendly, well-documented, standard in most environments |
| Public IPv4 | Required for SSH from both Cloud Shell and Termius |
| No private network | Not needed for single-node labs — avoids unnecessary complexity |
| No extra volumes | Default disk is sufficient; lab environment is disposable |
| Firewall (SSH + ICMP) | Limits attack surface while preserving full admin access |

### Firewall rules

| Protocol | Port/Type | Action |
|---|---|---|
| TCP | 22 (SSH) | Allow |
| ICMP | — | Allow |
| All others | — | Deny |

ICMP is allowed to enable `ping` for basic troubleshooting.

---

## SSH Access Setup

### Generate a key pair (from Cloud Shell)

```bash
ssh-keygen -t ed25519 -C "cloudshell"
```

This creates two files in `~/.ssh/`:

| File | Purpose |
|---|---|
| `id_ed25519` | Private key — never share or move this |
| `id_ed25519.pub` | Public key — copied to the VPS |

Ed25519 is the preferred algorithm: modern, secure, and fast.

### Add the public key to the VPS

Either paste the public key into Hetzner's interface during server creation, or append it manually after the fact:

```bash
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
```

This enables passwordless authentication from Cloud Shell to the VPS.

---

## Cloud Shell Setup

Google Cloud Shell provides a browser-based terminal with a persistent home directory — useful for accessing the lab from networks where installing SSH clients is not possible.

### What persists vs. what resets

| Path / Resource | Persists? |
|---|---|
| `~/.ssh/` | Yes |
| Scripts and configs in `~/` | Yes |
| `/etc/hosts` | No — resets on each session |
| Installed system packages | No — resets on each session |

Because system-level files reset, all configuration should live in user-space (`~/` or `~/.ssh/`).

---

## SSH Config

Storing connection details in `~/.ssh/config` creates a persistent shortcut that works every session without relying on system files like `/etc/hosts`.

File: `~/.ssh/config`

```
Host ubuntu1
    HostName 23.88.36.107
    User root
    IdentityFile ~/.ssh/id_ed25519
```

| Directive | Purpose |
|---|---|
| `Host ubuntu1` | Short alias used to invoke the connection |
| `HostName` | Maps the alias to the actual IP address |
| `User root` | Avoids specifying the username each time |
| `IdentityFile` | Forces the correct key when multiple keys are present |

With this in place, connecting is simply:

```bash
ssh ubuntu1
```

Instead of:

```bash
ssh root@23.88.36.107
```

---

## Multi-Access Setup

Two independent paths to the VPS:

| Client | Method | Best for |
|---|---|---|
| Termius | SSH client on local machine | Home use, full control |
| Google Cloud Shell | Browser-based terminal | Work networks, no install required |

Both authenticate with the same SSH key pair and use the same `~/.ssh/config` alias. This provides redundancy — if one path is unavailable, the other still works.

---

## Next Steps

- **Add a second VPS** — practice SSH between nodes, simulate real infrastructure
- **Harden SSH** — disable password authentication, optionally change the default port
- **Build repeatable labs** — users and permissions, services, networking, LFCS domain practice
