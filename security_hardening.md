# Security & Hardening Cheat Sheet

## 1. User & Permission Hardening

- Use least privilege principle.
- Remove or lock unused accounts:
```
usermod -L username  # lock
usermod -s /usr/sbin/nologin username  # disable login
```
- Ensure proper permissions:
```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

## 2. SSH Hardening

### /etc/ssh/sshd_config
```
PermitRootLogin no
PasswordAuthentication no
AllowUsers monkey
Port 2222
```
Restart:
```
systemctl restart ssh
```

- Use key-based auth.
- Change default port.
- Disable root login.

## 3. Firewall
### nftables example
```
nft add table inet filter
nft add chain inet filter input { type filter hook input priority 0; policy drop; }
nft add chain inet filter forward { type filter hook forward priority 0; policy drop; }
nft add chain inet filter output { type filter hook output priority 0; policy accept; }
nft add rule inet filter input ct state established,related accept
nft add rule inet filter input tcp dport 22 accept
nft list ruleset
```
- Only allow necessary ports.
- Save rules for persistence:
```
nft list ruleset > /etc/nftables.conf
systemctl enable nftables
systemctl start nftables
```

## 4. Fail2Ban
```
sudo apt install fail2ban
sudo systemctl enable --now fail2ban
```
- Protect SSH and other services from brute force attacks.

## 5. Updates & Patching
```
sudo apt update && sudo apt upgrade -y
```
- Automate security updates:
```
sudo apt install unattended-upgrades
```

## 6. AppArmor / SELinux
- AppArmor: `sudo aa-status`, enforce profiles.
- SELinux (if used): `sestatus`, set to enforcing mode.

## 7. Network Hardening
- Disable unused services: `systemctl disable <service>`.
- Use TCP wrappers for legacy services.
- Monitor open ports: `ss -tulwn`.
- Rate-limit connections via nftables rules.

## 8. File Integrity
- Use `AIDE`:
```
sudo apt install aide
sudo aideinit
sudo aide --check
```
- Regularly check important configs: `/etc`, `/var/log`.

## 9. Logging & Monitoring
- Centralize logs: rsyslog, journald.
- Watch `/var/log/auth.log`, `/var/log/syslog`.
- Tools: `logwatch`, `auditd`.

## 10. Password Policies
- Enforce complexity in `/etc/security/pwquality.conf`.
- Use `chage` to expire passwords.
- Avoid root password; rely on sudo.

## 11. Kernel & Sysctl Hardening
- Disable IP forwarding if not needed:
```
echo 0 > /proc/sys/net/ipv4/ip_forward
```
- Harden network settings in `/etc/sysctl.conf`:
```
net.ipv4.conf.all.rp_filter = 1
net.ipv4.tcp_syncookies = 1
net.ipv4.icmp_echo_ignore_broadcasts = 1
```
```
sysctl -p
```

## 12. Malware & Rootkit Detection
- Install `rkhunter` or `chkrootkit`:
```
sudo apt install rkhunter chkrootkit
sudo rkhunter --check
```

## 13. Backup Security
- Encrypt backups with `gpg` or `borg`.
- Store off-site.

## 14. System Auditing
- `auditd` rules:
```
sudo apt install auditd
sudo auditctl -w /etc/passwd -p wa
sudo ausearch -f /etc/passwd
```

## 15. Tips & Best Practices
- Disable unnecessary kernel modules.
- Disable USB if not required.
- Minimize installed packages.
- Use intrusion detection (OSSEC, Wazuh) for critical systems.
- Regularly review sudoers and cron jobs.
- Test security measures periodically.

