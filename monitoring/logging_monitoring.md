# Logging & Monitoring Cheat Sheet

## 1. System Logs Overview
Linux logs live mainly in `/var/log/` and `journalctl` (systemd).

### Key Log Files
| File | Description |
|------|-------------|
| `/var/log/syslog` | General system logging (Debian) |
| `/var/log/auth.log` | SSH, sudo, authentication |
| `/var/log/kern.log` | Kernel messages |
| `/var/log/Xorg.0.log` | X11 logs |
| `/var/log/dmesg` | Boot + kernel ring buffer (static copy) |

---

## 2. journalctl (systemd logging)

| Command | Description |
|---------|-------------|
| `journalctl` | Show all logs |
| `journalctl -u ssh` | Logs for service ssh |
| `journalctl -u nginx -f` | Follow service logs |
| `journalctl -b` | Logs since last boot |
| `journalctl -k` | Kernel-only logs |
| `journalctl -p err` | Priority filter (err, warning, info, etc.) |
| `journalctl --since "1 hour ago"` | Time-based filtering |
| `journalctl --disk-usage` | Show journal storage |

### Clear journal logs
```
sudo journalctl --vacuum-size=200M
sudo journalctl --vacuum-time=7d
```

---

## 3. dmesg (Kernel Ring Buffer)

| Command | Description |
|---------|-------------|
| `dmesg` | Dump kernel messages |
| `dmesg -T` | Human-readable timestamps |
| `dmesg | grep usb` | Filter USB events |

---

## 4. Monitoring Processes

### top
Basic system monitor.

| Keybindings | Action |
|-------------|--------|
| `M` | Sort by memory |
| `P` | Sort by CPU |
| `k` | Kill a process |
| `1` | Show per-core CPU |

### htop
Better interactive monitor.

### ps
| Command | Description |
|---------|-------------|
| `ps aux` | Full process list |
| `ps -ef` | Alternative listing |
| `ps aux --sort=-%cpu` | Sort by CPU |

---

## 5. Monitoring System Resources

### free
| Command | Description |
|---------|-------------|
| `free -h` | Memory usage |

### vmstat
| Command | Description |
|---------|-------------|
| `vmstat 1` | System load, memory, IO, every 1s |

### iostat
| Command | Description |
|---------|-------------|
| `iostat -xz 1` | Disk I/O detailed stats every 1s |

### mpstat
| Command | Description |
|---------|-------------|
| `mpstat -P ALL 1` | Per-CPU stats |

### pidstat
| Command | Description |
|---------|-------------|
| `pidstat 1` | CPU/mem per PID |

---

## 6. Network Monitoring

### ss (socket statistics)
| Command | Description |
|---------|-------------|
| `ss -tulpn` | Listening sockets |
| `ss -ta` | All TCP connections |

### iftop
Shows real-time bandwidth usage.

### nethogs
Group bandwidth usage by process.

### tcpdump
| Command | Description |
|---------|-------------|
| `tcpdump -i eth0` | Capture packets |
| `tcpdump -nn -i eth0 port 80` | Capture HTTP traffic |

---

## 7. Log Rotation (logrotate)
Handles rotating, compressing, and deleting old logs.

### Config Locations
- `/etc/logrotate.conf`
- `/etc/logrotate.d/*`

### Example snippet
```
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
}
```

---

## 8. System Health & Diagnostics

### systemctl
| Command | Description |
|---------|-------------|
| `systemctl status service` | Check service status |
| `systemctl --failed` | List failed services |

### sar (sysstat)
| Command | Description |
|---------|-------------|
| `sar -n DEV 1` | Network device stats |
| `sar -r 1` | Memory stats |

---

## 9. Hardware & Sensors

### sensors
| Command | Description |
|---------|-------------|
| `sensors` | CPU/GPU temperatures |

### smartctl
| Command | Description |
|---------|-------------|
| `smartctl -a /dev/sda` | Full SMART report |
| `smartctl -t short /dev/sda` | Run test |

---

## 10. Alerts & Notifications (CLI)

### mailx
```
echo "disk full" | mail -s "Alert" user@example.com
```

### notify-send (GUI)
```
notify-send "Backup completed"
```

---

## 11. Useful Tricks
- Use `watch -n 1 '<command>'` for live updates.
- Use `grep -i error -R /var/log/` to search logs.
- Combine `journalctl -f` with grep: `journalctl -f | grep ssh`.
- Use `pgrep` + `pkill` for quick process targeting.
- Monitor disk usage: `df -h`, `du -sh *`.