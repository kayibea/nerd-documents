# Hardware Info & Sensors Cheat Sheet

## 1. CPU Information

### lscpu
```
lscpu
```
Shows CPU model, cores, threads, flags, virtualization support.

### /proc/cpuinfo
```
cat /proc/cpuinfo
```
Detailed per-core info.

---

## 2. Memory Information

### free
```
free -h
```
Human-readable RAM usage.

### /proc/meminfo
```
cat /proc/meminfo
```
Detailed kernel memory stats.

---

## 3. PCI Devices

### lspci
```
lspci
lspci -v
lspci -nn
```
Drivers, vendor IDs, detailed hardware info.

---

## 4. USB Devices

### lsusb
```
lsusb
lsusb -v
```
Shows connected USB devices.

### dmesg for USB events
```
dmesg | grep -i usb
```

---

## 5. Storage Devices

### lsblk
```
lsblk -f
```
Filesystem, mountpoints, UUIDs.

### blkid
```
sudo blkid
```
Detailed block device metadata.

### smartctl
```
sudo smartctl -a /dev/sda
```
SMART health info.

### hdparm
```
sudo hdparm -I /dev/sda
```
Drive capabilities + cached settings.

---

## 6. Full Hardware Overview

### lshw
```
sudo lshw -short
sudo lshw
```
Massive hardware dump, full system overview.

### hwinfo
```
sudo hwinfo --short
```
Alternative to lshw.

---

## 7. BIOS/UEFI & Firmware

### dmidecode
```
sudo dmidecode
sudo dmidecode -t bios
```
BIOS version, vendor, serial numbers.

---

## 8. Sensors & Temperatures

### sensors
```
sensors
```
CPU/GPU temps, fan speeds, voltages.
Requires:
```
sudo apt install lm-sensors
sudo sensors-detect
```

### watch sensors
```
watch -n1 sensors
```

---

## 9. GPU Information

### Integrated / general
```
lspci | grep -i vga
```

### NVIDIA
```
nvidia-smi
```
Temps, usage, memory.

### AMD
```
sudo apt install radeontop
radeontop
```

### Intel
```
sudo apt install intel-gpu-tools
intel_gpu_top
```

---

## 10. Battery & Power

### upower
```
upower -i /org/freedesktop/UPower/devices/battery_BAT0
```

### acpi
```
acpi -V
```
Battery status, temps.

---

## 11. Network Hardware

### NIC details
```
ip link
ethtool eth0
```

### Wireless cards
```
ilist
```
Or:
```
lspci | grep -i wireless
```

---

## 12. Kernel Drivers

### lsmod
```
lsmod
```
Shows loaded modules.

### modinfo
```
modinfo <module>
```
Details about a kernel driver.

---

## 13. Motherboard & Sensors

### dmidecode (board info)
```
sudo dmidecode -t baseboard
```

### fancontrol
```
sudo pwmconfig
```
Fan curve management.

---

## 14. Useful Tricks
- Use `watch -n1 '<command>'` for real-time updates.
- Combine `lspci -k` to see which kernel driver is used.
- Find hardware bottlenecks using `dmesg`.
- Use `smartctl -t long /dev/sdX` for a complete disk self-test.
- Check temps under load using `stress-ng` + `sensors`.

