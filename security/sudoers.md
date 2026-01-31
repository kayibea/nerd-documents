# sudoers Cheatsheet

---

## What sudoers Is
- Policy language for **who can run what, as whom**
- Enforces **least privilege**
- Provides **auditing** and **environment control**

Not just "sudo on/off".

---

## Files & Tools

### Main Files
```text
/etc/sudoers
/etc/sudoers.d/*
```

### ALWAYS Edit With
```bash
visudo
visudo -f /etc/sudoers.d/deploy
```

Never edit sudoers with a normal editor.

---

## Basic Syntax
```sudoers
user  host = (runas) options: command
```

Example:
```sudoers
alice ALL=(root) /usr/bin/systemctl restart nginx
```

---

## Users vs Groups

### User Rule
```sudoers
alice ALL=(root) ALL
```

### Group Rule (Preferred)
```sudoers
%admins ALL=(root) ALL
```

---

## NOPASSWD (Automation)
```sudoers
deploy ALL=(root) NOPASSWD: /usr/bin/systemctl restart nginx
```

Multiple commands:
```sudoers
deploy ALL=(root) NOPASSWD: /usr/bin/systemctl restart nginx, /usr/bin/systemctl reload nginx
```

---

## Least Privilege Patterns

### Service Control Only
```sudoers
web ALL=(root) NOPASSWD: /usr/bin/systemctl restart nginx
```

### Backup User
```sudoers
backup ALL=(root) NOPASSWD: /usr/bin/rsync, /usr/bin/tar
```

### Package Updates Only
```sudoers
ops ALL=(root) NOPASSWD: /usr/bin/apt update, /usr/bin/apt upgrade
```

---

## Command Restrictions (IMPORTANT)

### Always Use Full Paths
```sudoers
alice ALL=(root) /usr/bin/vim
```

Bad:
```sudoers
alice ALL=(root) vim
```

(PATH injection risk)

---

## Wildcards (Danger Zone)
```sudoers
alice ALL=(root) /usr/bin/systemctl * nginx
```

⚠ Many binaries allow shell escapes.
Wildcards often mean **root shell by accident**.

---

## Prevent Shell Escapes

Some commands are unsafe even if restricted:
- `vim`
- `less`
- `man`
- `tar`
- `find`

If it can run `!sh`, it *is* a shell.

---

## Run As Other Users
```sudoers
alice ALL=(postgres) /usr/bin/psql
```

Usage:
```bash
sudo -u postgres psql
```

---

## Environment Control

### Secure Defaults
```sudoers
Defaults env_reset
Defaults secure_path="/usr/sbin:/usr/bin:/sbin:/bin"
```

### Preserve Specific Variables
```sudoers
Defaults env_keep += "SSH_AUTH_SOCK"
```

---

## Disable TTY Requirement (Automation)
```sudoers
Defaults:ci !requiretty
```

---

## Timeouts
```sudoers
Defaults timestamp_timeout=5
```

Disable caching:
```sudoers
Defaults timestamp_timeout=0
```

---

## Logging & Auditing

Default logs via journald:
```bash
journalctl -u sudo
```

Log input/output (powerful):
```sudoers
Defaults log_input, log_output
```

Logs stored under:
```text
/var/log/sudo-io/
```

---

## Testing Rules
```bash
sudo -l
sudo -l -U alice
```

Dry-run mental model before trusting rules.

---

## Common Footguns

- Allowing `ALL` commands
- Allowing editors as root
- Using wildcards blindly
- Forgetting full paths
- No logging
- No backups of sudoers

---

## Safe Pattern: sudoers.d/

```bash
/etc/sudoers.d/
  deploy
  backup
  monitoring
```

Each file:
```bash
chmod 440 /etc/sudoers.d/*
```

---

## Emergency Recovery Tip

If locked out but have root console:
```bash
pkexec visudo
```

---

## When NOT to Use sudoers
- You want a root shell → use `su -`
- Disposable VMs
- Single-user laptop

---

## Docs
```bash
man sudo
man sudoers
visudo -c
```

