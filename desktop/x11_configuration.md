# System Configuration Cheatsheet

---

## 1. Date / Time / Clock

### View Time
```bash
date
```

### Systemd-based Time Control
```bash
timedatectl status
sudo timedatectl set-time "2025-12-13 14:30:00"
sudo timedatectl set-timezone Europe/Paris
sudo timedatectl set-ntp true
```

### Hardware Clock
```bash
sudo hwclock --systohc  # system -> hw
sudo hwclock --hctosys  # hw -> system
```

---

## 2. Locale / Language

### Check Current Locale
```bash
locale
```

### Set Locale
```bash
sudo localectl status
sudo localectl set-locale LANG=en_US.UTF-8
sudo localectl set-keymap us
```

### Generate Locales
```bash
sudo nano /etc/locale.gen  # uncomment needed locales
sudo locale-gen
```

---

## 3. Hostname / Network Identity

### View / Set Hostname
```bash
hostnamectl status
sudo hostnamectl set-hostname myhost
```

### Static Host Mapping
```bash
sudo nano /etc/hosts
127.0.0.1 localhost myhost
```

### DNS
```bash
cat /etc/resolv.conf
sudo systemctl restart systemd-resolved
```

---

## 4. System Services

### Start / Stop / Enable Services
```bash
sudo systemctl start service
sudo systemctl stop service
sudo systemctl enable service
sudo systemctl disable service
```

### Status & Listing
```bash
systemctl status service
systemctl list-unit-files
systemctl list-units --type=service
```

---

## 5. Networking & Firewall

### Interface Status
```bash
ip link
ip addr show
```

### Bring Up / Down
```bash
sudo ip link set eth0 up
sudo ip link set eth0 down
```

### Firewall (nftables)
```bash
sudo nft list ruleset
sudo nft add rule inet filter input tcp dport 22 accept
```

---

## 6. Swap / Memory

### View Swap / Memory
```bash
swapon -s
free -h
```

### Enable / Disable Swap
```bash
sudo mkswap /dev/sdXn
sudo swapon /dev/sdXn
sudo swapoff /dev/sdXn
```

### fstab Swap Entry
```text
/dev/sdXn none swap sw 0 0
```

---

## 7. Login / Autologin

### Enable Autologin (systemd getty)
```bash
sudo nano /etc/systemd/system/getty@tty1.service.d/override.conf
# [Service]
# ExecStart=
# ExecStart=-/sbin/agetty --autologin user %I $TERM
sudo systemctl daemon-reexec
```

### Shell Profiles
```bash
~/.bash_profile
~/.profile
# put PATH, aliases, exports here
```

---

## 8. X11 Configuration (Runtime)

### Display / Monitor (xrandr)
```bash
xrandr --output HDMI-1 --mode 2560x1440 --right-of eDP-1
xrandr --output HDMI-1 --off
xrandr --output eDP-1 --rotate left
```

### Input Devices (xinput)
```bash
xinput list
xinput disable 12
xinput set-prop 12 "libinput Tapping Enabled" 1
```

### Keyboard Layout (setxkbmap)
```bash
setxkbmap us,fr
setxkbmap -option grp:alt_shift_toggle
setxkbmap -option caps:escape
```

### X Resources (xrdb)
```bash
xrdb -merge ~/.Xresources
xrdb -query
```

### Desktop Utilities (xdg-utils)
```bash
xdg-open https://example.com
xdg-mime default nvim.desktop text/plain
xdg-user-dir DOWNLOAD
```

### Persistence
Add to `.xinitrc` or `.xprofile`:
```bash
setxkbmap -option caps:escape
xrdb -merge ~/.Xresources
~/.screenlayout/default.sh
```

---

## 9. Misc / Utilities

### Aliases / Shell Options
```bash
alias ll='ls -la'
shopt -s histappend
```

### Power Management
```bash
tlp stat
sudo powertop
cat /sys/class/power_supply/BAT0/capacity
```

---

## Docs
```bash
man timedatectl
man hwclock
man localectl
man hostnamectl
man systemctl
man xrandr
man xinput
man setxkbmap
man xrdb
man xdg-open
```

