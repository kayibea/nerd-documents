# age Cheatsheet

---

## What is age (Quick)
- Modern, simple file encryption tool
- Replaces many GPG use-cases
- No web-of-trust, no keyservers, no metadata soup
- Opinionated by design

Use it when you want **encrypt/decrypt files**, not manage identities.

---

## Installation (Debian)
```bash
apt install age
```

---

## Key Types
- **X25519 key pair** (file encryption)
- **SSH public keys** (reuse existing infra)
- **Passphrases** (supported, discouraged)

---

## Generate Key Pair
```bash
age-keygen > key.txt
```

```text
# public key: age1...
AGE-SECRET-KEY-1...
```

### Split Keys (DO THIS)
```bash
grep '^# public key:' key.txt
sed -n 's/^AGE-SECRET-KEY-.*//p' key.txt
```

Save:
- `key.txt` → **private**, 0600
- public key → safe to share

```bash
chmod 600 key.txt
```

---

## Encrypt File (Public Key)
```bash
age -r age1qv... -o secrets.txt.age secrets.txt
```

Multiple recipients:
```bash
age -r age1... -r age1... -o data.age data
```

---

## Decrypt File
```bash
age -d -i key.txt secrets.txt.age > secrets.txt
```

Or via env:
```bash
export AGE_SECRET_KEY=$(grep '^AGE-SECRET-KEY' key.txt)
age -d secrets.txt.age
```

---

## Use SSH Keys (Recommended)

### Encrypt with SSH Public Key
```bash
age -R ~/.ssh/id_ed25519.pub -o file.age file
```

### Decrypt (automatic)
```bash
age -d file.age > file
```

Works with:
- `ssh-agent`
- hardware keys

---

## Recipient Files

### Create recipients.txt
```text
age1abc...
age1def...
```

### Encrypt Using File
```bash
age -R recipients.txt -o secrets.age secrets
```

---

## Passphrase Mode (Avoid If Possible)
```bash
age -p -o file.age file
```

Decrypt:
```bash
age -d file.age
```

Notes:
- Uses scrypt
- No identity separation
- Easy to screw up operationally

---

## Armor (ASCII Output)
```bash
age -a -r age1... -o file.age.txt file
```

---

## stdin / stdout (Pipelines)
```bash
tar cz secrets/ | age -r age1... > secrets.tar.gz.age
```

Decrypt:
```bash
age -d secrets.tar.gz.age | tar xz
```

---

## Editor Workflow
```bash
age -d secrets.age | $EDITOR
```

Better:
```bash
age -d secrets.age > /tmp/secrets
$EDITOR /tmp/secrets
age -r age1... -o secrets.age /tmp/secrets
shred -u /tmp/secrets
```

---

## Permissions (DO NOT IGNORE)
```bash
chmod 600 key.txt
chmod 700 ~/.config/age
```

---

## Common Mistakes
- Losing private key (no recovery)
- Using passphrase mode for automation
- Committing `.age` files without key backup
- Treating age like GPG (it is not)

---

## When NOT to Use age
- Need signatures / identity proof
- Need key revocation infrastructure
- Need email encryption

Use **GPG** instead.

---

## age vs GPG (Brutal Summary)
| Feature | age | GPG |
|------|-----|-----|
| Simplicity | Excellent | Terrible |
| File encryption | Excellent | OK |
| Signatures | No | Yes |
| Automation | Excellent | Painful |
| UX | Clean | Historical artifact |

---

## Docs
```bash
man age
man age-keygen
```

