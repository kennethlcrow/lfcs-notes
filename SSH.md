# SSH

> Part of [[Networking]] | [[00 - LFCS MOC]]

---

## Connecting

```bash
ssh username@IP_or_hostname        # basic connection
ssh -p 2222 username@host          # non-standard port
ssh -i ~/.ssh/mykey user@host      # specify private key

exit                               # end session
```

---

## Key-Based Authentication

More secure than password auth. The public key goes on the server; the private key stays on your machine.

```bash
# Generate a key pair (on your local machine)
ssh-keygen -t ed25519 -C "comment"      # modern, preferred
ssh-keygen -t rsa -b 4096 -C "comment" # RSA alternative

# Generated files:
~/.ssh/id_ed25519        # private key — never share
~/.ssh/id_ed25519.pub    # public key — copy to server

# Copy public key to remote server
ssh-copy-id username@host
# Manually: append ~/.ssh/id_ed25519.pub to ~/.ssh/authorized_keys on the server

# Correct permissions (required or SSH will refuse key auth)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_ed25519
```

---

## SSH Server Configuration

Config file: `/etc/ssh/sshd_config`

```bash
sudo vim /etc/ssh/sshd_config
```

Key settings:

```
Port 22                          # change to reduce automated attacks
PermitRootLogin no               # disable direct root login (best practice)
PasswordAuthentication no        # disable passwords, keys only
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys

AllowUsers aaron jane            # whitelist specific users
```

```bash
# Apply changes
sudo systemctl reload ssh.service   # or: sudo systemctl restart ssh.service

# Test config before reloading
sudo sshd -t
```

> **Warning:** If you disable `PasswordAuthentication`, make sure your key is in `authorized_keys` first or you'll lock yourself out.

---

## SSH Tunnels

### Local Port Forwarding

Forward a local port to a remote service. Useful for accessing services behind a firewall.

```bash
ssh -L 8080:localhost:80 user@remotehost
# Now: localhost:8080 → remotehost:80
```

### Remote Port Forwarding

Expose a local service on the remote host.

```bash
ssh -R 9090:localhost:3000 user@remotehost
# Now: remotehost:9090 → your localhost:3000
```

### Dynamic (SOCKS proxy)

```bash
ssh -D 1080 user@remotehost
# Configure browser to use SOCKS5 proxy at localhost:1080
```

---

## SCP & SFTP

```bash
# Copy file TO remote
scp file.txt user@host:/remote/path/

# Copy file FROM remote
scp user@host:/remote/file.txt ./local/

# Copy directory recursively
scp -r mydir/ user@host:/remote/

# Interactive SFTP session
sftp user@host
```

---

## SSH Agent

Avoid typing your key passphrase repeatedly:

```bash
eval $(ssh-agent)                 # start agent
ssh-add ~/.ssh/id_ed25519         # add key to agent
ssh-add -l                        # list loaded keys
```

---

## sshd Service

```bash
systemctl status ssh.service      # check status (Ubuntu: ssh, RHEL: sshd)
sudo systemctl enable --now ssh.service   # enable + start
sudo systemctl restart ssh.service        # restart after config changes
```

---

## Exam Tips

> **Exam tip:** On Ubuntu the service is `ssh.service`; on RHEL/CentOS it's `sshd.service`.

> **Exam tip:** `ssh-copy-id` is the fastest way to install a public key. Permissions on `~/.ssh/` and `authorized_keys` must be correct or key auth silently fails.

> **Exam tip:** Always test `sshd_config` changes with `sudo sshd -t` before reloading — a syntax error will prevent the daemon from restarting.

---

## Related Notes

- [[Firewalls]] — allow port 22 through the firewall
- [[SSL Certificates]] — TLS is separate from SSH keys
- [[Networking]]
