# Lab - Vagrant Setup

## Overview

Two Ubuntu 20.04 (focal64) VMs managed by Vagrant and VirtualBox.

| VM | IP |
|---|---|
| ubuntu1 | 192.168.56.101 |
| ubuntu2 | 192.168.56.102 |

Both VMs are provisioned automatically via `ubuntu.yml` (Ansible) and a shell provisioner in the Vagrantfile.

---

## Vagrant Commands

| Command | Description |
|---|---|
| `vagrant up` | Start all VMs |
| `vagrant halt` | Stop all VMs |
| `vagrant destroy` | Delete all VMs |
| `vagrant provision` | Re-run provisioners on running VMs |
| `vagrant ssh ubuntu1` | SSH into ubuntu1 via Vagrant (no key needed) |
| `vagrant box outdated` | Check if a newer base box is available |
| `vagrant box update` | Download the latest base box |
| `vagrant box prune` | Remove old box versions to free disk space |

---

## Provisioning

Provisioning runs automatically on `vagrant up` when VMs are first created. To re-run on existing VMs:

```powershell
vagrant provision
# or
vagrant up --provision
```

### Ansible — ubuntu.yml
Handles:
- `apt` package cache update and upgrade
- Sets `PasswordAuthentication yes` in `/etc/ssh/sshd_config`

### Shell provisioner — Vagrantfile
Handles:
- Sets `ChallengeResponseAuthentication no` in `/etc/ssh/sshd_config`

> Both are required for laptop → VM SSH with password authentication to work.

---

## SSH — Laptop to VM Setup (Windows)

### Why this is needed
Vagrant VMs use key-based authentication by default. The Vagrant-generated private key lives inside the project folder and is used by `vagrant ssh`. To SSH directly from PowerShell you need either:
- Your own keypair copied to the VM (key-based), or
- Password authentication enabled (handled by provisioners above)

### Step 1 — Check for an existing keypair on the laptop

```powershell
ls ~/.ssh
```

Look for `id_ed25519` and `id_ed25519.pub`. If they exist, skip to Step 3.

### Step 2 — Generate a keypair (if needed)

```powershell
ssh-keygen -t ed25519
```

This creates two files in `C:\Users\<you>\.ssh\`:
- `id_ed25519` — private key, stays on the laptop
- `id_ed25519.pub` — public key, copied to the VMs

> **Key concept:** The public key goes on any server you want to access. The private key never leaves your laptop.

### Step 3 — Copy your public key to the VMs

`ssh-copy-id` is not available on Windows, so do this manually. Use Vagrant's own private key to get in the first time and append your public key to `authorized_keys`:

```powershell
type ~/.ssh/id_ed25519.pub | ssh -i "C:\Users\luffy\projects\vagrant\lfcs\.vagrant\machines\ubuntu1\virtualbox\private_key" vagrant@192.168.56.101 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

type ~/.ssh/id_ed25519.pub | ssh -i "C:\Users\luffy\projects\vagrant\lfcs\.vagrant\machines\ubuntu2\virtualbox\private_key" vagrant@192.168.56.102 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### Step 4 — Connect

```powershell
ssh vagrant@192.168.56.101
ssh vagrant@192.168.56.102
```

No password or key flag needed — your laptop key is now authorised.

> **Common mistakes:**
> - Typo in IP address causes a timeout — double check `192.168.56.x`
> - `ssh 192.168.56.101` defaults to your Windows username — always specify `vagrant@`

---

## SSH — VM to VM

SSH between ubuntu1 and ubuntu2 works out of the box via Vagrant's provisioned keys. From ubuntu1:

```bash
ssh vagrant@192.168.56.102
```

---

## sshd_config Reference

Both of these lines must be set correctly on the VMs for laptop SSH to work:

```
PasswordAuthentication yes          # set by Ansible (ubuntu.yml)
ChallengeResponseAuthentication no  # set by shell provisioner (Vagrantfile)
```

If SSH stops working after rebuilding VMs, check these first:

```bash
sudo grep -E 'PasswordAuthentication|ChallengeResponseAuthentication' /etc/ssh/sshd_config
```
