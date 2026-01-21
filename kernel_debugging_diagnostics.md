# Kernel Debugging & Diagnostics Cheatsheet

---

## Scope
This document groups **kernel‑level diagnostics tools** commonly used together:
- `dmesg`
- `journalctl` (kernel view)
- `/proc` and `/sys`
- `lsmod`, `modprobe`, `modinfo`
- `udevadm`
- hardware‑related helpers

This is **below userland**, above firmware.

---

## dmesg — Kernel Ring Buffer

### Basic Usage
```bash
dmesg
dmesg | less
```

### Human‑Readable Timestamps
```bash
dmesg -T
```

### Follow New Messages (Live)
```bash
dmesg -w
```

### Filter by Level
```bash
dmesg --level=err,warn
dmesg -l crit,alert,emerg
```

Levels:
- emerg
- alert
- crit
- err
- warn
- notice
- info
- debug

---

## journalctl — Kernel Logs (Persistent)

### Kernel Messages Only
```bash
journalctl -k
```

### Follow Kernel Logs
```bash
journalctl -kf
```

### Previous Boot
```bash
journalctl -k -b -1
```

### Time Filtering
```bash
journalctl -k --since "10 minutes ago"
```

**Rule of thumb**:
- `dmesg` → current ring buffer
- `journalctl -k` → historical view

---

## Boot Debugging

### Full Boot Timeline
```bash
systemd-analyze blame
systemd-analyze critical-chain
```

### Early Boot Failures
```bash
dmesg | head
journalctl -k -b
```

---

## Hardware Detection

### PCI Devices
```bash
lspci -nn
lspci -k
```

### USB Devices
```bash
lsusb
lsusb -t
```

### Block Devices
```bash
lsblk
blkid
```

Correlate failures with:
```bash
dmesg | grep -i pci
dmesg | grep -i usb
dmesg | grep -i ata
```

---

## Kernel Modules

### List Loaded Modules
```bash
lsmod
```

### Module Info
```bash
modinfo e1000e
```

### Load / Unload
```bash
modprobe e1000e
modprobe -r e1000e
```

### Debug Module Load Failures
```bash
dmesg | tail
```

---

## udev & Hotplug

### Monitor Events (Live)
```bash
udevadm monitor --kernel --udev
```

### Query Device Info
```bash
udevadm info --query=all --name=/dev/sda
```

### Reload Rules
```bash
udevadm control --reload
udevadm trigger
```

Use when:
- disks not appearing
- permissions wrong
- devices renamed

---

## /proc — Runtime Kernel State

### CPU Info
```bash
cat /proc/cpuinfo
```

### Memory Info
```bash
cat /proc/meminfo
```

### Interrupts
```bash
cat /proc/interrupts
```

### Kernel Cmdline
```bash
cat /proc/cmdline
```

---

## /sys — Hardware Control Interface

### CPU Frequency
```bash
ls /sys/devices/system/cpu/
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
```

### Power / Thermal
```bash
ls /sys/class/thermal/
cat /sys/class/thermal/thermal_zone0/temp
```

Write operations here affect hardware.

---

## Memory & OOM Debugging

### OOM Killer Logs
```bash
dmesg | grep -i oom
journalctl -k | grep -i oom
```

### Memory Pressure
```bash
free -h
vmstat 1
```

---

## Disk & Filesystem Errors

```bash
dmesg | grep -i ext4
dmesg | grep -i btrfs
dmesg | grep -i xfs
```

Common causes:
- bad cables
- dying disks
- power loss

---

## Network Driver Debugging

```bash
dmesg | grep -i eth
dmesg | grep -i wlan
```

Combine with:
```bash
ip link
ethtool eth0
```

---

## Access Control (Security Note)

Modern kernels restrict dmesg:
```bash
sysctl kernel.dmesg_restrict
```

Enable restriction:
```bash
sysctl -w kernel.dmesg_restrict=1
```

---

## Common Diagnostic Patterns

### Device Missing
1. `lsblk / lspci / lsusb`
2. `dmesg | tail`
3. `journalctl -k`
4. `udevadm monitor`

### Boot Slow
1. `systemd-analyze blame`
2. `journalctl -k -b`

### Random Freezes
1. `journalctl -k -b -1`
2. Look for `WARN`, `BUG`, `Oops`

---

## When dmesg Is Not Enough

Use:
- `strace` → syscalls
- `perf` → performance
- `ftrace` → kernel tracing

---

## Docs
```bash
man dmesg
man journalctl
man udev
man proc
man sysfs
```

