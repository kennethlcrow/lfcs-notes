# LDAP Authentication

> Part of [[Users & Groups]] | [[00 - LFCS MOC]]

---

## What is LDAP?

**LDAP** (Lightweight Directory Access Protocol) is a protocol for accessing a centralized directory of users, groups, and other objects. Instead of managing accounts on every individual server, users are defined once in a directory (like OpenLDAP or Active Directory) and every server authenticates against it.

**Benefits:**
- Single place to manage user accounts
- One password works across all servers
- Centralized access control

---

## Key Components

| Component | Role |
|---|---|
| **LDAP server** | Stores the directory (OpenLDAP, Active Directory, FreeIPA) |
| **SSSD** | System Security Services Daemon — client-side auth broker |
| **PAM** | Pluggable Authentication Modules — integrates SSSD into login |
| **NSS** | Name Service Switch — allows `getent passwd` to find LDAP users |

---

## SSSD — The Client Daemon

SSSD is the standard way to connect a Linux system to LDAP (or Kerberos, AD, FreeIPA).

```bash
sudo apt install sssd sssd-ldap    # install
sudo systemctl enable --now sssd   # start and enable
systemctl status sssd              # check status
```

### /etc/sssd/sssd.conf

```ini
[sssd]
services = nss, pam
config_file_version = 2
domains = LDAP

[domain/LDAP]
id_provider = ldap
auth_provider = ldap
ldap_uri = ldap://192.168.0.10
ldap_search_base = dc=example,dc=com
ldap_default_bind_dn = cn=admin,dc=example,dc=com
ldap_default_authtok = secretpassword
ldap_tls_reqcert = demand
ldap_tls_cacert = /etc/ssl/certs/ca.crt
```

```bash
sudo chmod 600 /etc/sssd/sssd.conf    # must be 600 or SSSD refuses to start
sudo systemctl restart sssd
```

---

## PAM Integration

SSSD installs PAM modules automatically, but you can verify:

```bash
grep sssd /etc/pam.d/common-auth
grep sssd /etc/pam.d/common-account
grep sssd /etc/pam.d/common-session
```

To set up automatically (recommended):

```bash
sudo pam-auth-update                   # interactive; enable "SSSD" option
```

---

## NSS Integration

NSS allows `id`, `getent passwd`, and `ls -l` to resolve LDAP users.

```bash
cat /etc/nsswitch.conf
```

Should include `sss` for the relevant databases:

```
passwd:   files systemd sss
group:    files systemd sss
shadow:   files sss
```

---

## Testing LDAP Authentication

```bash
# Look up an LDAP user
getent passwd ldapuser

# Look up an LDAP group
getent group ldapgroup

# Test login (should authenticate against LDAP)
su - ldapuser

# Query LDAP server directly
ldapsearch -x -H ldap://192.168.0.10 \
  -b "dc=example,dc=com" \
  "(uid=jane)"
```

---

## Creating Home Directories on First Login

LDAP users may not have home directories on the local machine. Enable auto-creation via PAM:

```bash
sudo vim /etc/pam.d/common-session
# Add:
session required pam_mkhomedir.so skel=/etc/skel umask=0077
```

---

## FreeIPA / Red Hat IdM

FreeIPA is a full identity management suite combining LDAP, Kerberos, DNS, and CA.

```bash
# Client enrollment
sudo apt install freeipa-client
sudo ipa-client-install --domain=ipa.example.com \
  --server=ipa-server.example.com \
  --realm=IPA.EXAMPLE.COM
```

---

## Active Directory Integration

Joining Ubuntu to an Active Directory domain:

```bash
sudo apt install sssd-ad adcli realmd

# Discover domain
realm discover ad.example.com

# Join domain
sudo realm join -U administrator ad.example.com

# Allow AD users to log in
sudo realm permit --all                    # allow all AD users
sudo realm permit jane@ad.example.com      # allow specific user

# Verify
realm list
getent passwd jane@ad.example.com
```

---

## LDAP Data Model (Quick Reference)

```
dc=example,dc=com                  # Domain Component (top level)
├── ou=Users                       # Organizational Unit
│   ├── uid=jane                   # User entry
│   └── uid=aaron
└── ou=Groups
    └── cn=developers              # Group entry
```

Common LDAP attribute names:
- `uid` — username
- `cn` — common name
- `dn` — distinguished name (full path in directory)
- `sn` — surname
- `userPassword` — password hash
- `uidNumber`, `gidNumber` — Unix UID/GID

---

## Exam Tips

> **Exam tip:** SSSD's config file must be `chmod 600` — SSSD will refuse to start if it's world-readable.

> **Exam tip:** After configuring SSSD, test with `getent passwd <ldapuser>` before testing actual login. If that fails, the NSS/PAM plumbing isn't wired up yet.

> **Exam tip:** `realm join` (from the `realmd` package) is the easiest way to join AD — it configures SSSD, PAM, and NSS automatically.

> **Exam tip:** LDAP users won't have local home dirs by default. Add `pam_mkhomedir.so` to `/etc/pam.d/common-session` to create them on first login.

---

## Related Notes

- [[Local Users & Groups]] — local accounts vs. LDAP accounts
- [[SSL Certificates]] — LDAP over TLS requires a trusted CA cert
- [[Users & Groups]]
