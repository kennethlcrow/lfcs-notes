# systemd

> Part of [[Operations & Deployment]] | [[00 - LFCS MOC]]

---

## What is systemd?

**systemd** is both the name of the Linux init system and a larger collection of tools for managing services, logging, timers, mounts, and more. When Linux boots, systemd is the first process started (PID 1) and it manages everything that follows.

**systemd units** are text files that describe how to start, stop, and manage a resource. Common unit types:

| Type | Purpose |
|---|---|
| `service` | Manages a daemon or application |
| `timer` | Schedules when a service runs |
| `socket` | Activates a service on incoming connections |
| `mount` | Manages filesystem mount points |

---

## Service Management

```bash
systemctl status ssh.service           # show status, PID, recent logs
systemctl start ssh.service            # start now
systemctl stop ssh.service             # stop now
systemctl restart ssh.service          # stop then start (disruptive)
systemctl reload ssh.service           # reload config without full restart
systemctl reload-or-restart ssh.service  # try reload, fall back to restart

systemctl enable ssh.service           # autostart at boot
systemctl disable ssh.service          # don't autostart at boot
systemctl enable --now ssh.service     # enable + start immediately
systemctl disable --now ssh.service    # disable + stop immediately

systemctl is-enabled ssh.service       # check if enabled
systemctl is-active ssh.service        # check if currently running
```

> After installing a new daemon, run `sudo systemctl enable --now <service>` to both enable autostart and start it immediately.

---

## Masking Services

Some services will restart themselves even after you stop and disable them (started by another service). **Masking** prevents a service from ever being started:

```bash
sudo systemctl mask atd.service        # prevent any start (even by other services)
sudo systemctl unmask atd.service      # restore normal behavior
```

A masked service will fail even if explicitly started:
```
Failed to start atd.service: Unit atd.service is masked.
```

---

## Listing & Exploring Units

```bash
systemctl list-units --type service --all   # all service units (any state)
systemctl list-dependencies                 # tree view of active/inactive units
systemctl cat ssh.service                   # view a service's unit file
sudo systemctl edit --full ssh.service      # edit a service unit file
sudo systemctl revert ssh.service           # revert edits to factory default
```

**Reading `systemctl list-dependencies` output:**
- 🟢 Green circle = unit is active (good)
- ⚪ White circle = unit is inactive (may be a problem for critical services)

Critical services that should always be green: `ssh.service`, `cron.service`, `atd.service`

---

## Unit File Structure

Key directives in a service unit file:

```ini
[Unit]
Description=OpenBSD Secure Shell server

[Service]
ExecStart=/usr/sbin/sshd -D $SSHD_OPTS    # command to start the daemon
ExecReload=/usr/sbin/sshd -t              # command to reload config
Restart=on-failure                         # restart if it crashes

[Install]
WantedBy=multi-user.target
```

Common `Restart=` values: `no`, `on-failure`, `always`, `on-abnormal`

---

## Logs with journalctl

The systemd journal collects logs from all services in one place.

```bash
journalctl -u ssh.service          # logs for a specific service unit
journalctl -u ssh.service -f       # follow (live)
journalctl -p err                  # filter by priority
journalctl -b 0                    # current boot only
journalctl -b -1                   # previous boot
journalctl -S '2024-03-03 01:00'   # since a specific time
journalctl -S 01:00 -U 02:00       # between two times
journalctl -e                      # jump to end of output

# Make journal persistent across reboots:
sudo mkdir /var/log/journal/
```

Priority levels: `emerg alert crit err warning notice info debug`

> **Tip:** If `journalctl` shows no output, prefix with `sudo` — you may need root or membership in the `adm`/`sudo` group.

---

## Exam Tips

> **Exam tip:** Know `enable --now` and `disable --now` — they do two things in one command and save time.

> **Exam tip:** `reload` is gentler than `restart` — it applies new config without dropping connections. Not all services support it; use `reload-or-restart` when unsure.

> **Exam tip:** If a service won't start, check `systemctl status <service>` first, then `journalctl -u <service>` for deeper logs.

---

## Related Notes

- [[Scheduling Tasks]] — systemd timers as an alternative to cron
- [[Process Management]]
- [[Operations & Deployment]]
