# Self-Hosted Git over SSH (Minimal, Secure Setup)

This document describes a **general-purpose, minimal, secure Git hosting setup** using standard Unix tools.

It intentionally avoids web interfaces, databases, or vendor-specific platforms, focusing instead on primitives that are already present on most Unix-like systems.

The design goals are:

- No web interface
- SSH-only access
- Least privilege by default
- Clear, conventional filesystem layout
- Suitable for *any* Git repositories
- Easy to back up, audit, and reason about

This is **not** meant to replace GitHub, GitLab, or similar platforms. It is meant to be:

> A small, boring, understandable Git service you fully control.

---

## Table of Contents

1. Overview
2. Threat Model
3. Filesystem Layout
4. Service Account (`git` user)
5. Git Shell (`git-shell`)
6. SSH Key Restrictions
7. Repository Layout (Bare Repositories)
8. Client Usage
9. Permissions & Ownership
10. Server-Side Hardening (Optional)
11. Backups
12. Common Failure Modes

---

## 1. Overview

Git is fundamentally a **file-based protocol**. When combined with SSH, it does not require:

- A background daemon
- A web server
- A database
- Any long-running Git-specific service

This setup relies only on:

- `git` (for repository storage and protocol handling)
- `ssh` (for transport and authentication)
- `git-shell` (for command restriction)
- Unix filesystem permissions (for isolation)

Everything else is optional.

---

## 2. Threat Model

This setup assumes:

- You trust the host machine
- You trust SSH cryptography and key handling
- You want to limit damage if an SSH key is compromised

Explicitly out of scope:

- Anonymous access
- Untrusted multi-tenant environments
- Fine-grained, per-repository ACLs
- Web-based authentication flows

If an attacker gains:

- **Root access** → full system compromise (out of scope)
- **An authorized SSH key** → access limited to Git operations only

They **cannot**:

- Obtain an interactive shell
- Execute arbitrary commands
- Access files outside the Git repositories

---

## 3. Filesystem Layout

The layout follows the Filesystem Hierarchy Standard (FHS).

```text
/srv/git/
├── example.git/
│   ├── HEAD
│   ├── objects/
│   ├── refs/
│   └── hooks/
└── .ssh/
    └── authorized_keys
```

### Why `/srv/git`

- `/srv` is intended for **data served by system services**
- Git repositories are served over SSH
- Keeps service data separate from user home directories
- Simplifies backup and restore procedures

Common alternatives:

- `/var/lib/git` (distribution-managed setups)
- `/data/git` (custom storage or separate disks)
- `/home/git` (acceptable, but less semantically clean)

---

## 4. Service Account (`git` user)

Repositories are owned by a **dedicated system account**.

```bash
sudo useradd -r -m -d /srv/git -s /usr/bin/git-shell git
sudo passwd -l git
```

### Flag breakdown

- `-r` — create a system account (non-human)
- `-m` — create the home directory
- `-d /srv/git` — place service data under `/srv`
- `-s /usr/bin/git-shell` — prevent interactive logins

### Why a dedicated account

- Enforces least privilege
- Creates a clear ownership boundary
- Prevents accidental shell access
- Simplifies auditing and backups

---

## 5. Git Shell (`git-shell`)

`git-shell` is a restricted shell included with Git.

It allows only:

- `git-upload-pack` (clone/fetch)
- `git-receive-pack` (push)

It denies:

- Interactive shells
- Arbitrary command execution
- Non-Git file transfers

Attempting to log in directly:

```bash
ssh git@host
```

Results in:

```text
fatal: Interactive shell is disabled
```

This behavior is expected and desirable.

---

## 6. SSH Key Restrictions

SSH access is managed via:

```text
/srv/git/.ssh/authorized_keys
```

Example entry:

```text
command="git-shell -c \"$SSH_ORIGINAL_COMMAND\"",no-agent-forwarding,no-port-forwarding,no-X11-forwarding ssh-ed25519 AAAA...
```

### What this enforces

- Forces `git-shell` even if the login shell is misconfigured
- Disables SSH agent forwarding
- Disables port forwarding
- Disables X11 forwarding

Each key is restricted to:

> Git protocol operations only.

### Required permissions

```bash
sudo chown -R git:git /srv/git/.ssh
sudo chmod 700 /srv/git/.ssh
sudo chmod 600 /srv/git/.ssh/authorized_keys
```

Incorrect permissions will cause SSH to reject the key.

---

## 7. Repository Layout (Bare Repositories)

Repositories are created as **bare repositories**.

```bash
sudo -u git git init --bare /srv/git/example.git
```

Why bare repositories:

- No working tree to corrupt
- Safe for concurrent access
- Matches how Git servers operate
- Prevents accidental local edits

---

## 8. Client Usage

Clone a repository:

```bash
git clone git@host:/srv/git/example.git
```

Standard Git operations work normally:

```bash
git pull
git push
```

No special client configuration is required beyond SSH access.

---

## 9. Permissions & Ownership

All repositories and SSH configuration must be owned by the `git` user:

```bash
sudo chown -R git:git /srv/git
```

Clients never write files directly; all writes occur through the Git protocol.

---

## 10. Server-Side Hardening (Optional)

### Server-side hooks

Git hooks can enforce policy such as:

- Branch protection
- Commit message rules
- File type restrictions

Example `pre-receive` hook skeleton:

```bash
#!/bin/sh
# inspect incoming refs here
exit 0
```

Hooks live in:

```text
/srv/git/example.git/hooks/
```

Hooks must be executable to run.

---

## 11. Backups

This setup **requires explicit backups**.

Minimal example:

```bash
rsync -a --delete /srv/git /backup/srv-git
```

Recommended practices:

- Maintain offline backups
- Maintain off-site backups
- Periodically test restore procedures

A secondary Git remote (another server or hosted provider) can be used purely as off-site redundancy.

---

## 12. Common Failure Modes

### SSH reports "Permission denied"

Usually caused by:

- Incorrect `.ssh` permissions
- Wrong file ownership
- Mismatched SSH keys

### Push is rejected

Possible causes:

- Hook rejection
- Non-fast-forward updates
- Repository permission issues

### Data loss

Most commonly due to:

- Missing backups
- Disk failure
- Human error

Backups matter more than secrecy.

---

## Closing Notes

This setup is:

- Minimal
- Auditable
- Unix-native
- Intentionally boring

Its strength comes from using well-understood primitives instead of complex platforms.

If you understand every component described here, you are operating a Git service — not pretending to.

