# Network Configuration

> Part of [[Networking]] | [[00 - LFCS MOC]]

---

## Viewing Network Info

```bash
ip a                               # show all interfaces and IP addresses
ip a show enp1s0                   # show a specific interface
ip r                               # show routing table
ip r show default                  # show default gateway

# Find the network interface for a specific IP range
ip a | grep '10.0.0'
```

**Reading `ip a` output:**

```
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 10.0.0.1/24 brd 10.0.0.255 scope global enp1s0
```

- Interface name: `enp1s0`
- IP/CIDR: `10.0.0.1/24`
- Broadcast: `10.0.0.255`

**Finding your default route (for NAT/forwarding):**
```bash
ip r | grep default
# default via 10.11.12.1 dev enp1s0 ...
# → enp1s0 is the interface used for external traffic
```

---

## Configuring Network Interfaces (Ubuntu — Netplan)

Ubuntu uses **Netplan** (`/etc/netplan/*.yaml`) for network configuration.

```bash
ls /etc/netplan/                              # find config file
sudo vim /etc/netplan/01-netcfg.yaml
```

**Static IP example:**

```yaml
network:
  version: 2
  ethernets:
    enp1s0:
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
      dhcp4: false
```

**DHCP example:**

```yaml
network:
  version: 2
  ethernets:
    enp1s0:
      dhcp4: true
```

```bash
sudo netplan apply                 # apply changes
sudo netplan try                   # apply with 120s rollback window (safe testing)
```

---

## Temporary IP Changes (ip command)

These **do not persist** across reboots:

```bash
sudo ip addr add 192.168.1.200/24 dev enp1s0    # add IP to interface
sudo ip addr del 192.168.1.200/24 dev enp1s0    # remove IP
sudo ip link set enp1s0 up                       # bring interface up
sudo ip link set enp1s0 down                     # bring interface down

sudo ip route add default via 192.168.1.1        # add default route
sudo ip route del default                        # remove default route
```

---

## Checking Network Services

```bash
sudo ss -ltunp                     # what's listening on which ports
sudo ss -tunp                      # all active connections
```

---

## Network Bonding

**Bonding** combines multiple physical interfaces into one virtual interface for resilience or increased throughput.

**Bonding modes:**

| Mode | Name | Purpose |
|---|---|---|
| 0 | round-robin | Distribute traffic sequentially |
| 1 | active-backup | One active, others on standby |
| 2 | XOR | Same src/dst pair always uses same interface |
| 3 | broadcast | Send data on all interfaces simultaneously |
| 4 | IEEE 802.3ad (LACP) | Increases throughput above single NIC |
| 5 | adaptive transmit LB | Load balance outgoing traffic |
| 6 | adaptive load balancing | Load balance both directions |

**Netplan bonding example:**

```yaml
network:
  version: 2
  bonds:
    bond0:
      interfaces: [enp1s0, enp2s0]
      parameters:
        mode: active-backup
      dhcp4: true
```

---

## Network Bridging

**Bridging** connects two networks so devices on both can communicate as if on the same network.

- Bridge = "controller"
- Interfaces added to bridge = "ports"

**Netplan bridge example:**

```yaml
network:
  version: 2
  bridges:
    br0:
      interfaces: [enp1s0, enp2s0]
      dhcp4: true
```

---

## Reverse Proxy & Load Balancing (Nginx)

### Reverse Proxy

```bash
sudo apt install nginx
sudo vim /etc/nginx/sites-available/proxy.conf
```

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://1.1.1.1;
        include proxy_params;       # pass real client IP headers
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/proxy.conf /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t                       # test config
sudo systemctl reload nginx.service
```

### Load Balancer

```nginx
upstream mywebservers {
    least_conn;                     # route to least busy server
    server 1.2.3.4 weight=3;        # this server gets 3x the traffic
    server 5.6.7.8;                 # default weight=1
    server 10.20.30.40 backup;      # only used if others are down
}

server {
    listen 80;
    location / {
        proxy_pass http://mywebservers;
    }
}
```

Load balancing methods: `round-robin` (default), `least_conn`, `ip_hash`

---

## Exam Tips

> **Exam tip:** `ip a` and `ip r` are your first tools for inspecting network state. `ss -ltunp` shows you what's listening.

> **Exam tip:** Netplan changes require `sudo netplan apply`. Use `sudo netplan try` for a safe test with automatic rollback.

> **Exam tip:** For bonding, Mode 1 (active-backup) is the simplest for redundancy; Mode 4 (IEEE 802.3ad) for throughput.

---

## Related Notes

- [[Firewalld]] — control what traffic is allowed
- [[DNS & Hostname]] — name resolution
- [[Kernel Runtime Parameters]] — IP forwarding for NAT/routing
- [[Networking]]
