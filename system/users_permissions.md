# Bash/Linux Users & Permissions Cheat Sheet

## 1. User Management

| Command | Description |
|---------|-------------|
| `useradd <name>` | Create new user (basic) |
| `adduser <name>` | Create user with guided setup (Debian preferred) |
| `userdel <name>` | Delete user |
| `userdel -r <name>` | Delete user + home directory |
| `usermod -aG <group> <user>` | Add user to group (append) |
| `usermod -s /bin/bash <user>` | Change login shell |
| `usermod -l <new> <old>` | Rename user |
| `passwd <user>` | Set/change password |

## 2. Group Management

| Command | Description |
|---------|-------------|
| `groupadd <group>` | Create group |
| `groupdel <group>` | Remove group |
| `groupmod -n <new> <old>` | Rename group |
| `groups <user>` | Show user's groups |

## 3. Permission Basics

| Symbolic | Octal | Meaning |
|----------|--------|---------|
| `r` | 4 | Read |
| `w` | 2 | Write |
| `x` | 1 | Execute |

### Typical octal values
- `644` → owner read/write, group+others read
- `600` → owner read/write only
- `755` → owner rwx, group+others rx
- `700` → owner-only rwx

## 4. chmod (Change Permissions)

| Command | Description |
|---------|-------------|
| `chmod 644 file` | Set numeric mode |
| `chmod u+x script.sh` | Add execute for owner |
| `chmod g-w file` | Remove write for group |
| `chmod o-rwx file` | Remove all perms for others |
| `chmod -R 755 dir/` | Recursive change |

## 5. chown (Change Owner)

| Command | Description |
|---------|-------------|
| `chown user file` | Change file owner |
| `chown user:group file` | Change owner + group |
| `chown -R user:group dir/` | Recursive |

## 6. chgrp (Change Group)

| Command | Description |
|---------|-------------|
| `chgrp staff file` | Set owning group |
| `chgrp -R staff dir/` | Recursive |

## 7. Special Permissions (suid, sgid, sticky)

| Mode | Meaning |
|------|---------|
| `suid` (4xxx) | Run with owner's privileges |
| `sgid` (2xxx) | Run with group's privileges |
| `sticky` (1xxx) | Only owner can delete files in directory |

### Examples
- `chmod 4755 file` → setuid + 755 perms
- `chmod 2755 dir` → setgid on directory
- `/tmp` has sticky bit: `drwxrwxrwt`

## 8. sudo & Privilege Escalation

| Command | Description |
|---------|-------------|
| `sudo <cmd>` | Run command as root |
| `sudo -u <user> <cmd>` | Run as another user |
| `sudo -s` | Root shell with user's env |
| `sudo -i` | Full root login shell |

### sudoers
Edit via:
```
visudo
```
Examples:
```
user ALL=(ALL) NOPASSWD: /usr/bin/systemctl
%admins ALL=(ALL) ALL
```

## 9. File Ownership Analysis

| Command | Description |
|---------|-------------|
| `ls -l` | Show owners + perms |
| `namei -l /path/file` | Breakdown of permissions along path |
| `stat file` | Full stat info (owner, group, modes) |

## 10. Access Control Lists (ACLs)

### Commands
| Command | Description |
|---------|-------------|
| `getfacl file` | View ACLs |
| `setfacl -m u:user:rwx file` | Add ACL entry |
| `setfacl -m g:group:rx file` | Group ACL |
| `setfacl -x u:user file` | Remove ACL entry |
| `setfacl -R -m u:user:rwX dir/` | Recursive ACL |

### ACL Example
```
setfacl -m u:john:rw file.txt
```

## 11. Tips & Tricks

- Prefer `adduser` over `useradd` on Debian for sane defaults.
- Check permissions step-by-step with `namei -l /path/to/file`.
- Use ACLs when classic rwx perms aren’t enough.
- Use `chmod o-rwx` to quickly lock files.
- Use setgid on shared directories: `chmod 2775 shared/`.
- Always edit sudoers with `visudo` to avoid locking yourself out.
