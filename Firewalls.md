# Firewalls

> Part of [[Networking]] | [[00 - LFCS MOC]]

---

## Overview

Linux uses a **packet filtering firewall** built into the kernel (via the Netfilter framework). Several tools sit on top of it:

| Tool | Notes |
|---|---|
| `ufw` | Uncomplicated Firewall — simple, Ubuntu default |
| `iptables` | Older but widely supported; rules auto-translated to nft |
| `nft` / `nftables` | Modern replacement for iptables |
| `firewalld` | Zone-based firewall; RHEL/CentOS default |

> The LFCS course primarily uses **UFW** on Ubuntu. Know UFW well; know iptables basics.

---

## UFW — Uncomplicated Firewall

```bash
sudo ufw status                    # show rules and enabled/disabled state
sudo ufw status verbose            # more detail
sudo ufw enable                    # turn on firewall
sudo ufw disable                   # turn off firewall
```

### Allowing & Denying

```bash
sudo ufw allow 22                  # allow port 22 (SSH) — TCP + UDP
sudo ufw allow 22/tcp              # TCP only
sudo ufw allow ssh                 # by service name (same as port 22)
sudo ufw allow 80                  # HTTP
sudo ufw allow 443                 # HTTPS
sudo ufw allow 8080/tcp

sudo ufw deny 23                   # deny telnet
sudo ufw deny from 192.168.1.10    # deny all traffic from an IP

# Allow from specific IP to specific port
sudo ufw allow from 10.0.0.0/24 to any port 22

# Routing/forwarding rules
sudo ufw route allow from 10.0.0.0/24 to 192.168.0.5
```

### Removing Rules

```bash
sudo ufw delete allow 80           # delete by rule
sudo ufw status numbered           # list rules with numbers
sudo ufw delete 3                  # delete rule #3
```

### Reset

```bash
sudo ufw reset                     # clear all rules and disable
```

---

## iptables — Packet Filter

iptables works with **tables** and **chains**:

| Table | Purpose |
|---|---|
| `filter` | Default table — allow/deny packets |
| `nat` | Network Address Translation (port forwarding, masquerade) |
| `mangle` | Modify packet headers |

Key chains:
- `INPUT` — packets destined for this machine
- `OUTPUT` — packets leaving this machine
- `FORWARD` — packets passing through
- `PREROUTING` — modify before routing (DNAT)
- `POSTROUTING` — modify after routing (MASQUERADE)

### Basic Usage

```bash
sudo iptables -L                   # list filter rules
sudo iptables -L -v -n             # verbose, numeric
sudo iptables --list-rules --table nat    # list NAT rules

# Allow incoming SSH
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Drop all incoming HTTP
sudo iptables -A INPUT -p tcp --dport 80 -j DROP

# Flush (clear) all rules in filter table
sudo iptables -F

# Flush NAT table
sudo iptables --flush --table nat
```

### Port Forwarding with iptables

Enable IP forwarding first (see [[Kernel Runtime Parameters]]):
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Forward port 8080 → 192.168.0.5:80 (from 10.0.0.0/24 network):
```bash
# DNAT — redirect incoming packets
sudo iptables -t nat -A PREROUTING \
  -i enp1s0 -s 10.0.0.0/24 \
  -p tcp --dport 8080 \
  -j DNAT --to-destination 192.168.0.5:80

# MASQUERADE — rewrite source IP so return traffic works
sudo iptables -t nat -A POSTROUTING \
  -s 10.0.0.0/24 -o enp6s0 \
  -j MASQUERADE
```

> **Quick reference:** `man ufw-framework` has a "Full example" section showing the PREROUTING/POSTROUTING pattern.

### Making iptables Rules Persistent

```bash
sudo apt install iptables-persistent
sudo netfilter-persistent save      # save current rules
sudo netfilter-persistent reload    # reload saved rules
```

---

## nft — View Translated Rules

```bash
sudo nft list ruleset              # view all nft rules (iptables rules translated here too)
```

---

## Checking What's Listening

```bash
sudo ss -ltunp                     # see what ports are open
```

---

## Exam Tips

> **Exam tip:** UFW is simpler for basic allow/deny rules. `sudo ufw allow 22` before enabling the firewall — otherwise you'll lock yourself out over SSH.

> **Exam tip:** iptables rules are **not persistent by default**. Install `iptables-persistent` to save them across reboots.

> **Exam tip:** For port forwarding, you need TWO rules: a PREROUTING DNAT rule and a POSTROUTING MASQUERADE rule. IP forwarding must also be enabled in sysctl.

> **Exam tip:** `-i` = input interface (PREROUTING), `-o` = output interface (POSTROUTING).

---

## Related Notes

- [[Network Configuration]] — IP forwarding, interfaces
- [[Kernel Runtime Parameters]] — `net.ipv4.ip_forward`
- [[SSH]] — always allow port 22 before enabling a firewall
- [[Networking]]
