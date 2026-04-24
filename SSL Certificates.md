# SSL Certificates

> Part of [[Operations & Deployment]] | [[00 - LFCS MOC]]

---

## Concepts

**TLS/SSL certificates** authenticate servers and enable encrypted connections. In sysadmin work you'll often need to:
- Generate a self-signed certificate for internal use or testing
- Create a Certificate Signing Request (CSR) for a CA-signed cert
- Configure services (Nginx, Apache, etc.) to use a certificate

**Key files:**

| File | Description |
|---|---|
| `.key` | Private key — keep secret, never share |
| `.csr` | Certificate Signing Request — sent to a CA |
| `.crt` / `.pem` | Certificate — public, shared with clients |
| `.chain.pem` | Intermediate CA chain (if needed) |

---

## Generating a Self-Signed Certificate

Use `openssl` to create a private key and self-signed certificate in one step:

```bash
sudo openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 \
  -keyout /etc/ssl/private/myserver.key \
  -out /etc/ssl/certs/myserver.crt
```

**Options explained:**
- `req -x509` — create a self-signed certificate (skips CSR step)
- `-nodes` — no passphrase on the private key (required for unattended service start)
- `-days 365` — validity period
- `-newkey rsa:2048` — generate a new 2048-bit RSA key pair
- `-keyout` — where to save the private key
- `-out` — where to save the certificate

You'll be prompted for certificate fields (country, org, Common Name, etc.).

---

## Two-Step Process (Key + CSR + Cert)

Used when submitting to a real Certificate Authority:

```bash
# Step 1: Generate private key
openssl genrsa -out myserver.key 2048

# Step 2: Generate CSR
openssl req -new -key myserver.key -out myserver.csr

# Step 3: Self-sign (for testing) OR submit myserver.csr to a CA
openssl x509 -req -days 365 \
  -in myserver.csr \
  -signkey myserver.key \
  -out myserver.crt
```

---

## Inspecting Certificates

```bash
# View certificate details
openssl x509 -in myserver.crt -text -noout

# Check expiry date only
openssl x509 -in myserver.crt -noout -dates

# Verify a certificate matches its private key
openssl x509 -noout -modulus -in myserver.crt | md5sum
openssl rsa  -noout -modulus -in myserver.key | md5sum
# Outputs should match

# Test a live server's certificate
openssl s_client -connect example.com:443
```

---

## File Permissions

```bash
# Private key — only root should read it
sudo chmod 600 /etc/ssl/private/myserver.key
sudo chown root:root /etc/ssl/private/myserver.key

# Certificate — readable by all (public)
sudo chmod 644 /etc/ssl/certs/myserver.crt
```

---

## Configuring Nginx with SSL

```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate     /etc/ssl/certs/myserver.crt;
    ssl_certificate_key /etc/ssl/private/myserver.key;

    location / {
        root /var/www/html;
    }
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}
```

```bash
sudo nginx -t                              # test config
sudo systemctl reload nginx.service        # apply
```

---

## Common Locations

| Path | Purpose |
|---|---|
| `/etc/ssl/certs/` | Public certificates |
| `/etc/ssl/private/` | Private keys (restricted) |
| `/usr/local/share/ca-certificates/` | Add trusted CA certs here |
| `update-ca-certificates` | Rebuild the system CA trust store |

```bash
# Trust a new CA certificate system-wide
sudo cp myCA.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
```

---

## Exam Tips

> **Exam tip:** Remember `-nodes` when generating certs for services — without it, openssl adds a passphrase that will prevent the service from starting automatically.

> **Exam tip:** The modulus check (`md5sum` comparison) is the quick way to confirm a cert and key are a matched pair.

> **Exam tip:** Default cert/key locations for most distros are `/etc/ssl/certs/` and `/etc/ssl/private/`. Keep private keys at `600` permissions.

---

## Related Notes

- [[SSH]] — SSH uses its own key pairs, separate from TLS
- [[Operations & Deployment]]
