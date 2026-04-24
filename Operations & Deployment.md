# Operations & Deployment

> Exam weight: **25%** | Part of [[00 - LFCS MOC]]

---

## Domain Overview

Operations & Deployment covers managing system services, automating tasks, monitoring resources, and tuning the Linux kernel. It's the largest exam domain and heavily tests real sysadmin judgment.

### Topics

- [[systemd]] — managing services, units, and the init system
- [[Scheduling Tasks]] — cron, anacron, and `at`
- [[Process Management]] — monitoring and controlling processes
- [[Kernel Runtime Parameters]] — `sysctl` and persistent tuning
- [[SSL Certificates]] — creating and managing TLS/SSL certs

---

## Quick Reference

### Boot, Reboot, Shutdown

```bash
sudo systemctl reboot              # graceful reboot
sudo systemctl poweroff            # graceful shutdown
sudo systemctl reboot --force      # force reboot (programs won't close gracefully)
sudo systemctl reboot --force --force  # hard reset (last resort)

sudo shutdown 02:00                # shutdown at 2 AM
sudo shutdown -r +15               # reboot in 15 minutes
sudo shutdown -r +1 'Rebooting to apply kernel update'  # with wall message
```

### Logs

```bash
journalctl -u ssh.service          # logs for a specific service
journalctl -p err                  # filter by priority: alert crit debug emerg err info notice warning
journalctl -S '2024-03-03 01:00'   # since a specific time
journalctl -b 0                    # current boot
journalctl -b -1                   # previous boot
journalctl -f                      # follow (live)

grep -r 'sshd' /var/log/           # search traditional log files
tail -F /var/log/auth.log          # follow a log file live

last                               # login history
lastlog                            # last login for each user
```

### Resource Monitoring

```bash
df -h                              # disk space usage (human-readable)
du -sh /usr/                       # disk usage of a directory
free -h                            # RAM usage
uptime                             # CPU load averages (1, 5, 15 min)
lscpu                              # CPU details
lspci                              # PCI hardware details
```

### Filesystem Integrity

```bash
sudo xfs_repair -v /dev/vdb1       # check/repair XFS (must be unmounted)
sudo fsck.ext4 -v -f -p /dev/vdb2  # check/repair ext4 (must be unmounted)
```

---

## Exam Tips

> **Exam tip:** `systemctl list-dependencies` gives a quick health overview of important services. If something critical shows as inactive, `systemctl status <service>` and `journalctl -u <service>` are your first debugging tools.

> **Exam tip:** Changes made with `sysctl -w` are temporary. To persist across reboots, write to a `.conf` file in `/etc/sysctl.d/`.

> **Exam tip:** All changes must **persist after reboot** to count. Always confirm with `systemctl is-enabled` and test with `sysctl` after adding to `/etc/sysctl.d/`.

---

## Related Notes

- [[systemd]]
- [[Scheduling Tasks]]
- [[Process Management]]
- [[Kernel Runtime Parameters]]
- [[SSL Certificates]]
