# DNS & Hostname

> Part of [[Networking]] | [[00 - LFCS MOC]]

---

## Hostname Management

```bash
hostname                           # show current hostname
hostnamectl                        # show full hostname info (static, transient, pretty)
sudo hostnamectl set-hostname myserver.example.com   # set hostname persistently
```

Hostname is stored in `/etc/hostname`.

Types:
- **Static hostname** — persists across reboots, set in `/etc/hostname`
- **Transient hostname** — set by DHCP/mDNS at runtime (overrides static)
- **Pretty hostname** — human-friendly, can include spaces

---

## /etc/hosts

Maps hostnames to IP addresses locally, before DNS is consulted.

```bash
cat /etc/hosts
sudo vim /etc/hosts
```

```
127.0.0.1   localhost
127.0.1.1   myserver.example.com myserver
10.0.0.5    webserver1.internal webserver1
```

> Changes to `/etc/hosts` take effect immediately — no service restart needed.

---

## DNS Resolution

### /etc/resolv.conf

Specifies DNS servers to use:

```
nameserver 8.8.8.8
nameserver 1.1.1.1
search example.com
```

```bash
cat /etc/resolv.conf
```

> On systems using `systemd-resolved`, this file may be a symlink. Manage DNS via Netplan or `resolvectl` instead.

### /etc/nsswitch.conf

Controls the order of name resolution:

```
hosts: files dns mdns4_minimal
```

- `files` = check `/etc/hosts` first
- `dns` = then query DNS servers
- `mdns4_minimal` = then try mDNS

---

## Testing DNS

```bash
# Query DNS
dig example.com                    # full DNS query output
dig example.com A                  # A record (IPv4)
dig example.com AAAA               # IPv6 address
dig example.com MX                 # mail server
dig @8.8.8.8 example.com           # query specific DNS server

nslookup example.com               # simpler DNS query
nslookup example.com 8.8.8.8       # query specific server

host example.com                   # concise lookup

# Reverse lookup (IP to hostname)
dig -x 8.8.8.8
```

---

## systemd-resolved

Modern DNS resolver included in systemd:

```bash
resolvectl status                  # show current DNS settings and servers
resolvectl query example.com       # resolve a name
resolvectl flush-caches            # clear DNS cache

systemctl status systemd-resolved  # check service status
```

Configure via Netplan (`nameservers:` block) or `/etc/systemd/resolved.conf`.

---

## Time Synchronization (NTP)

Accurate time is critical for logs, certificates, and Kerberos auth.

```bash
timedatectl                        # show current time, timezone, NTP status
timedatectl list-timezones         # browse available timezones
sudo timedatectl set-timezone America/New_York   # set timezone

sudo timedatectl set-ntp true      # enable NTP synchronization
timedatectl show-timesync          # show NTP servers in use
timedatectl timesync-status        # show poll interval and root distance
```

### systemd-timesyncd

Ubuntu's default NTP client:

```bash
sudo apt install systemd-timesyncd   # install if missing
systemctl status systemd-timesyncd  # check status

# Configure NTP servers
sudo vim /etc/systemd/timesyncd.conf
# Uncomment and edit:
# NTP=0.us.pool.ntp.org 1.us.pool.ntp.org 2.us.pool.ntp.org 3.us.pool.ntp.org

sudo systemctl restart systemd-timesyncd
```

> Timezone format: `Continent/City` — use underscore for spaces: `America/New_York`

---

## Exam Tips

> **Exam tip:** `/etc/hosts` changes take effect immediately — no restart needed. Great for quick fixes or exam lab environments.

> **Exam tip:** After changing hostname with `hostnamectl`, update `/etc/hosts` too — many tools use the hostname to resolve back to `127.0.1.1`.

> **Exam tip:** NTP must be active for certificates and auth to work correctly. If `timedatectl` shows NTP inactive, run `sudo timedatectl set-ntp true`.

> **Exam tip:** DNS query order is in `/etc/nsswitch.conf`. If name resolution seems wrong, check there and `/etc/resolv.conf`.

---

## Related Notes

- [[Network Configuration]] — where DNS servers are set in Netplan
- [[SSL Certificates]] — certs break if system time is wrong
- [[Networking]]
