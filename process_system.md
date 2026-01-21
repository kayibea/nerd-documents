# Bash/Linux Process & System Management Cheat Sheet

## 1. Process Listing & Monitoring

| Command | Description | Tricks |
|---------|------------|--------|
| `ps` | Show processes | `ps aux` (all processes), `ps -ef` (standard format) |
| `top` | Interactive process monitor | Press `P` (CPU), `M` (memory), `k` to kill, `q` to quit |
| `htop` | Enhanced top with UI | `htop` (scroll, filter, sort, interactive kill) |
| `pgrep` | Find process by name | `pgrep -l firefox` (list PIDs) |
| `pidof` | Get PID of process | `pidof sshd` |

## 2. Controlling Processes

| Command | Description | Tricks |
|---------|------------|--------|
| `kill` | Terminate a process | `kill -SIGTERM 1234` or `kill -9 1234` for force kill |
| `killall` | Kill processes by name | `killall firefox` |
| `pkill` | Kill by pattern | `pkill -f python` |
| `nice` | Start process with priority | `nice -n 10 ./script.sh` |
| `renice` | Change priority of running process | `renice -n -5 -p 1234` |
| `jobs` | List background jobs in shell | `jobs -l` for PIDs |
| `fg` / `bg` | Bring job to foreground / background | `fg %1` |

## 3. System Information

| Command | Description | Tricks |
|---------|------------|--------|
| `uptime` | Show system uptime & load | `uptime` or `w` |
| `uname -a` | Kernel and system info | `uname -r` for kernel version |
| `dmesg` | Kernel logs | `dmesg | less` for scrolling, `dmesg | grep error` |
| `free` | Memory usage | `free -h` (human-readable), `free -m` (MB) |
| `vmstat` | System stats | `vmstat 2 5` (interval 2s, 5 times) |
| `uptime` | Load average | Shows load for 1,5,15 mins |
| `lsof` | List open files | `lsof -p 1234` (process), `lsof /path` |
| `watch` | Repeat command periodically | `watch -n 2 'ps aux | grep sshd'` |

## 4. Disk & I/O Monitoring

| Command | Description | Tricks |
|---------|------------|--------|
| `df -h` | Disk space usage | Human-readable format |
| `du -sh /path` | Directory size | Summarize folder size |
| `iostat` | CPU & I/O stats | `iostat -xz 2` for extended stats every 2s |
| `iotop` | Real-time I/O | Requires root, shows process I/O usage |
| `vmstat` | Virtual memory & processes | Combined CPU/memory stats |

## 5. Services & Daemons

| Command | Description | Tricks |
|---------|------------|--------|
| `systemctl status` | Check service status | `systemctl status ssh` |
| `systemctl start/stop/restart` | Control service | `systemctl restart apache2` |
| `systemctl enable/disable` | Auto-start control | `systemctl enable nginx` |
| `systemctl list-units --type=service` | List active services | `systemctl list-unit-files --type=service` for all |
| `journalctl` | View logs | `journalctl -u ssh` (service logs), `journalctl -f` (follow) |

## 6. System Load & Performance

| Command | Description | Tricks |
|---------|------------|--------|
| `uptime` | System load | Load averages for 1,5,15 mins |
| `top` / `htop` | CPU & memory | Sort by `%CPU` or `%MEM` |
| `sar` | Historical stats | `sar -u 1 5` CPU usage every 1s 5 times |
| `free` | RAM & swap | `free -h` human readable |
| `vmstat` | Memory, processes, CPU | `vmstat 2` updates every 2 seconds |
| `iostat` | Disk I/O | `iostat -xz 2` for detailed stats |
| `uptime` | Quick load check | `uptime`

## 7. Process Priorities & Scheduling

| Command | Description | Tricks |
|---------|------------|--------|
| `nice` | Launch with priority | `nice -n 10 command` (lower priority) |
| `renice` | Adjust priority of running process | `renice -n -5 -p 1234` |
| `at` | Schedule one-time job | `echo 'reboot' | at 22:00` |
| `cron` / `crontab` | Schedule recurring tasks | `crontab -e` edit, `crontab -l` list |

## 8. Tips & Tricks

- Combine `ps aux | grep process` to quickly find PIDs.  
- Use `top` or `htop` to interactively monitor CPU/memory usage.  
- Use `watch` to monitor changing stats like `df -h` or `ls -lh /tmp`.  
- Always check `journalctl -xe` after a failed service start.  
- Background long-running processes with `nohup command &` to keep them running after logout.  
- Use `lsof -i :PORT` to find which process is using a network port.