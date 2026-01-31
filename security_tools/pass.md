# pass (password-store) Cheatsheet

---

## What pass Is (and Is Not)
- Minimal password manager
- Plain-text files encrypted with **GPG** (or age via wrapper)
- Git-backed by default
- Not a vault, not a server, not magic

If this feels trivial: good. That’s the point.

---

## Installation (Debian)
```bash
apt install pass gnupg git
```

Optional:
```bash
apt install pass-otp xclip wl-clipboard
```

---

## Initialization

### GPG-backed Store (Default)
```bash
pass init KEYID
```

Multiple recipients:
```bash
pass init KEYID1 KEYID2
```

Directory:
```bash
~/.password-store/
```

Permissions (critical):
```bash
chmod 700 ~/.password-store
```

---

## Basic Usage

### Insert Password
```bash
pass insert email/github
```

Multiline (recommended):
```bash
pass insert -m servers/prod
```

Convention:
```
password
username: user
url: https://example.com
notes: ...
```

### Generate Password
```bash
pass generate email/github 32
```

No symbols:
```bash
pass generate -n email/github 24
```

### Show Password
```bash
pass email/github
```

Copy to clipboard (auto-clear):
```bash
pass -c email/github
```

---

## Listing & Search
```bash
pass
pass ls
pass find github
pass grep token
```

---

## Editing & Removal
```bash
pass edit email/github
pass rm email/github
```

---

## Git Integration (The Real Feature)

### Initialize Repo
```bash
pass git init
```

### Normal Workflow
```bash
pass git status
pass git commit -am "chore: update passwords"
pass git push
```

### Clone Existing Store
```bash
git clone git@host:password-store.git ~/.password-store
pass init KEYID
```

---

## Password Rotation
```bash
pass generate -i email/github 32
```

---

## OTP (TOTP / HOTP)

### Add OTP
```bash
pass otp insert email/github
```

### Show OTP
```bash
pass otp email/github
```

Clipboard:
```bash
pass otp -c email/github
```

---

## Environment Variables

Export password (careful):
```bash
export DB_PASS=$(pass db/prod)
```

Better:
```bash
DB_PASS=$(pass db/prod)
command --password "$DB_PASS"
unset DB_PASS
```

---

## Extensions

List:
```bash
pass extension
```

Common:
- `pass-otp`
- `pass-update`
- `pass-import`

---

## Using age Instead of GPG (Advanced)

Official `pass` does **not** support age natively.
Options:
- `rage` wrapper
- `pass-age` fork
- custom scripts

If you want *simple*: use `age` directly.

---

## Backup Strategy

Minimum viable:
```bash
tar cz ~/.password-store | age -r age1... > pass-backup.tgz.age
```

Also backup:
- GPG secret keys
- revocation certs

---

## Common Mistakes
- Not backing up GPG keys
- World-readable store
- Using passphrase-only GPG keys
- Clipboard leaks (X11)

---

## When pass Is the Wrong Tool
- Multiple devices + non-technical users
- Need sharing with access control
- Need web UI

Use Bitwarden / Vault instead.

---

## Docs
```bash
man pass
man pass-generate
man pass-otp
```

