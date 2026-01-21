# GPG (GnuPG) Cheatsheet

---

## Basics
```bash
gpg --version
gpg --list-keys
gpg --list-secret-keys
```

## Key Generation

### Modern Recommended (ed25519 / cv25519)
```bash
gpg --full-generate-key
```
Choose:
- Key type: `(9) ECC (sign and encrypt)`
- Curve: `Curve 25519`

### Quick Generate (non-interactive)
```bash
gpg --quick-generate-key "Name <email@example.com>" ed25519 sign 1y
gpg --quick-add-key "email@example.com" cv25519 encrypt 1y
```

## Key IDs & Fingerprints
```bash
gpg --fingerprint
gpg --list-keys --keyid-format LONG
```

## Export / Import Keys

### Export Public Key
```bash
gpg --export --armor email@example.com > pubkey.asc
```

### Export Private Key (Be Careful)
```bash
gpg --export-secret-keys --armor email@example.com > private.asc
```

### Import Key
```bash
gpg --import pubkey.asc
```

## Trust Model

### Edit Trust
```bash
gpg --edit-key email@example.com
trust
```

### Common Trust Levels
- 1 = I don’t know
- 2 = I do NOT trust
- 3 = Marginal
- 4 = Full
- 5 = Ultimate (your own key)

## Encrypt / Decrypt Files

### Encrypt for Recipient
```bash
gpg --encrypt --recipient email@example.com file.txt
```

### Encrypt + ASCII Armor
```bash
gpg --encrypt --armor -r email@example.com file.txt
```

### Decrypt
```bash
gpg --decrypt file.txt.gpg
gpg --output file.txt --decrypt file.txt.gpg
```

## Symmetric Encryption (Password-based)
```bash
gpg --symmetric file.txt
gpg --cipher-algo AES256 --symmetric file.txt
```

## Signatures

### Sign File (Binary)
```bash
gpg --sign file.txt
```

### Clear-sign (Readable)
```bash
gpg --clearsign file.txt
```

### Detached Signature (Best Practice)
```bash
gpg --detach-sign file.txt
```

### Verify Signature
```bash
gpg --verify file.txt.sig file.txt
```

## Keyserver Operations

```bash
gpg --keyserver hkps://keys.openpgp.org --search-keys email@example.com
gpg --keyserver hkps://keys.openpgp.org --recv-keys KEYID
gpg --send-keys KEYID
```

## Revocation Certificate (IMPORTANT)
```bash
gpg --output revoke.asc --gen-revoke KEYID
```
Store **offline**.

## Key Expiration
```bash
gpg --edit-key KEYID
expire
```

## Delete Keys

### Delete Secret Key First
```bash
gpg --delete-secret-keys KEYID
gpg --delete-keys KEYID
```

## Agent & Caching

### Check Agent
```bash
gpgconf --list-dirs agent-socket
echo RELOADAGENT | gpg-connect-agent
```

### Restart Agent
```bash
gpgconf --kill gpg-agent
gpgconf --launch gpg-agent
```

## gpg-agent.conf (~/.gnupg/gpg-agent.conf)
```conf
default-cache-ttl 600
max-cache-ttl 7200
enable-ssh-support
```

```bash
echo "enable-ssh-support" >> ~/.gnupg/gpg-agent.conf
gpgconf --kill gpg-agent
```

## GPG as SSH Key
```bash
export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
ssh-add -L
```

## Permissions (CRITICAL)
```bash
chmod 700 ~/.gnupg
chmod 600 ~/.gnupg/*
```

## Backup Strategy (Sane One)

Backup **offline**:
- `~/.gnupg`
- revocation certificate
- exported secret keys

```bash
tar -czf gpg-backup.tgz ~/.gnupg
```

## Common Problems

### "No secret key"
```bash
gpg --list-secret-keys
```

### Pinentry Issues
```bash
export GPG_TTY=$(tty)
```
Add to shell rc.

### Reset Broken State
```bash
gpgconf --kill all
```

## Git Integration
```bash
git config --global user.signingkey KEYID
git config --global commit.gpgsign true
git commit -S -m "feat: signed commit"
```

## Docs
```bash
man gpg
man gpg-agent
info gnupg
```

