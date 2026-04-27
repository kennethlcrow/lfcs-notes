# Networking

> Exam weight: **25%** | Part of [[00 - LFCS MOC]]

---

## Domain Overview

Networking is one of the two largest exam domains. It covers IP addressing fundamentals, configuring network interfaces, managing services listening on the network, firewalls, and DNS.

### Topics

- [[SSH]] — remote access, key-based auth, SSH tunnels
- [[Firewalls]] — packet filtering with UFW and iptables/nft
- [[Network Configuration]] — IP addresses, interfaces, routing, bonding/bridging
- [[DNS & Hostname]] — hostname management, DNS resolution, NTP time sync

---

## IP Addressing Quick Reference

### IPv4

```
192.168.1.101/24
└────────┘└──┘
 network   host
 prefix
```

- `/24` = first 24 bits are network prefix → hosts from `.0` to `.255` on same network
- `/16` = first 16 bits → `192.168.x.x` range

### IPv6

```
2001:0db8:0000:0000:0000:ff00:0042:8329
# Shortened to:
2001:db8::ff00:42:8329
```

- 128-bit address in 8 hexadecimal groups
- Leading zeros dropped, consecutive zero groups replaced with `::`

---

## Network Services

```bash
sudo ss -ltunp                     # listening sockets: TCP, UDP, numeric, with process
sudo netstat -ltunp                # same (older tool, may not be installed)

# Mnemonic: "tunnel programs" → -tunlp or -ltunp
# l=listening, t=tcp, u=udp, n=numeric, p=process
```

**Reading the output:**
- `127.0.0.1:3306` → only accessible locally (loopback)
- `0.0.0.0:22` → accessible from any external IP (IPv4)
- `[::]:22` → same, but IPv6

```bash
systemctl status ssh.service       # check status of a network service
ps PID                             # inspect a process by PID
sudo lsof -p PID                   # files/sockets open by that process
```

---

## Exam Tips

> **Exam tip:** `ss -ltunp` (or `ss -tunlp`) is your go-to for checking what's listening and on which port. Remember the mnemonic: **tunnel programs**.

> **Exam tip:** Know CIDR notation. `/24` = 256 hosts, `/16` = 65536 hosts, `/32` = single host.

> **Exam tip:** Firewall rules and iptables rules are **not persistent** by default. To persist, install `iptables-persistent` and run `sudo netfilter-persistent save`.

---

## Related Notes

- [[SSH]]
- [[Firewalls]]
- [[Network Configuration]]
- [[DNS & Hostname]]
