# SSH Cheatsheet

---

## Basics
```bash
ssh user@host
ssh host
ssh -p 2222 user@host
```

## Verbosity & Debugging
```bash
ssh -v user@host     # verbose
ssh -vv user@host    # more verbose
ssh -vvv user@host   # maximum debug
```

## Identity / Keys
```bash
ssh-keygen -t ed25519
ssh-keygen -t rsa -b 4096
ssh-copy-id user@host
ssh -i ~/.ssh/id_ed25519 user@host
```

## Agent
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh-add -l
ssh-add -D
```

## Config File (~/.ssh/config)
```sshconfig
Host myserver
  HostName example.com
  User deploy
  Port 2222
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes
```

```bash
ssh myserver
```

## Port Forwarding

### Local Forward (L)
```bash
ssh -L 8080:localhost:80 user@host
```

### Remote Forward (R)
```bash
ssh -R 9000:localhost:9000 user@host
```

### Dynamic / SOCKS Proxy (D)
```bash
ssh -D 1080 user@host
```

## Jump Hosts (ProxyJump)
```bash
ssh -J user@jump user@target
```

```sshconfig
Host target
  ProxyJump user@jump
```

## Tunnels Without Shell
```bash
ssh -N -L 3306:localhost:3306 user@host
```

## Command Execution
```bash
ssh user@host "uptime"
ssh user@host "systemctl status nginx"
```

## File Transfer

### SCP
```bash
scp file.txt user@host:/tmp/
scp user@host:/etc/nginx/nginx.conf .
scp -r dir/ user@host:/opt/
```

### SFTP
```bash
sftp user@host
put file.txt
get file.txt
```

### Rsync over SSH
```bash
rsync -avz dir/ user@host:/backup/
rsync -avz -e ssh user@host:/data/ ./data/
```

## Known Hosts
```bash
ssh-keygen -F host
ssh-keygen -R host
```

## Permissions (Very Important)
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/authorized_keys
```

## ControlMaster (Connection Multiplexing)
```sshconfig
Host *
  ControlMaster auto
  ControlPath ~/.ssh/cm-%r@%h:%p
  ControlPersist 10m
```

## Disable Password Auth (Server-side)
```conf
PasswordAuthentication no
PermitRootLogin no
```

```bash
systemctl reload sshd
```

## Security Flags
```bash
ssh -o StrictHostKeyChecking=ask user@host
ssh -o PasswordAuthentication=no user@host
```

## X11 Forwarding
```bash
ssh -X user@host
ssh -Y user@host
```

## Common Failure Fixes
```bash
ssh -o PreferredAuthentications=publickey user@host
ssh -o PubkeyAcceptedAlgorithms=+ssh-rsa user@host
```

## Useful One-liners
```bash
ssh user@host 'df -h'
ssh user@host 'journalctl -xe'
ssh user@host 'uptime'
```

## Docs
```bash
man ssh
man ssh_config
man sshd_config
```

