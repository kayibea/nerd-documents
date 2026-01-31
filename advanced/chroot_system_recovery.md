# chroot & System Recovery Cheatsheet

---

## Scope
This document focuses on **system recovery**, rescue environments, and offline repairs using:
- `chroot`
- live USB / rescue shells
- filesystem mounts
- bootloader recovery
- password, init, and package fixes

This is what you use when the system **does not boot**.

---

## What chroot Really Is

`chroot` changes the **apparent root directory** of a process.

It does **not**:
- virtualize the kernel
- isolate processes (not a container)
- protect you from mistakes

It **does**:
- let you operate on a broken system as if it were booted

---

## Typical Recovery Workflow (High Level)

1. Boot from **live USB / rescue ISO**
2. Identify disks and partitions
3. Mount target system
4. Bind critical pseudo-filesystems
5. `chroot`
6. Fix things (bootloader, passwd, packages)
7. Exit, unmount, reboot

---

## Identify Disks & Partitions

```bash
lsblk
blkid
fdisk -l
```

Know:
- root (`/`)
- boot (`/boot` or `/boot/efi`)
- separate `/home`

---

## Mount Target System

### Basic (Single Root Partition)
```bash
mount /dev/sdXn /mnt
```

### With Separate Boot
```bash
mount /dev/sdXn /mnt
mount /dev/sdYn /mnt/boot
```

### EFI Systems
```bash
mount /dev/sdZn /mnt/boot/efi
```

---

## Bind Pseudo‑Filesystems (Mandatory)

Without these, most tools will fail.

```bash
mount --bind /dev  /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys  /mnt/sys
```

Optional but useful:
```bash
mount --bind /run /mnt/run
```

---

## Enter chroot

```bash
chroot /mnt /bin/bash
```

You are now **inside the broken system**.

Verify:
```bash
ls /
cat /etc/os-release
```

---

## Common Recovery Tasks

---

### Fix Broken Packages (Debian/Ubuntu)

```bash
apt update
apt --fix-broken install
dpkg --configure -a
```

---

### Reinstall Kernel

```bash
apt install --reinstall linux-image-amd64
update-initramfs -u
```

---

### Regenerate initramfs Only

```bash
update-initramfs -c -k all
```

---

### Reset Root or User Password

```bash
passwd
passwd username
```

If PAM is broken, fix it before rebooting.

---

### Fix fstab Errors

Bad `fstab` = boot failure.

```bash
nano /etc/fstab
mount -a
```

Never reboot until `mount -a` is clean.

---

### Reinstall GRUB (BIOS)

```bash
grub-install /dev/sdX
update-grub
```

---

### Reinstall GRUB (UEFI)

```bash
grub-install \
  --target=x86_64-efi \
  --efi-directory=/boot/efi \
  --bootloader-id=GRUB
update-grub
```

---

### Fix Missing EFI Entries

```bash
efibootmgr -v
efibootmgr --create \
  --disk /dev/sdX \
  --part N \
  --label "Linux" \
  --loader "\\EFI\\GRUB\\grubx64.efi"
```

---

## Networking Inside chroot

DNS often missing.

```bash
cp /etc/resolv.conf /mnt/etc/resolv.conf
```

Verify:
```bash
ping -c 3 1.1.1.1
```

---

## Using systemd-nspawn (Better chroot)

Safer, more complete environment.

```bash
systemd-nspawn -D /mnt
```

Advantages:
- proper cgroups
- networking
- less weird breakage

---

## When chroot Is NOT Enough

You may need:
- filesystem repair
- hardware replacement
- kernel parameters

---

## Filesystem Repair (Offline)

### ext4
```bash
umount /dev/sdXn
e2fsck -f /dev/sdXn
```

### btrfs
```bash
btrfs check /dev/sdXn
```

### xfs
```bash
xfs_repair /dev/sdXn
```

---

## Emergency Without chroot (Init=/bin/sh)

Kernel boot param:
```text
init=/bin/sh
```

Then:
```bash
mount -o remount,rw /
```

Useful for:
- forgotten root password
- totally broken userspace

---

## Clean Exit (IMPORTANT)

```bash
exit
umount -R /mnt
reboot
```

Never yank power before unmounting.

---

## Common Mistakes

- forgetting `/proc` or `/sys`
- reinstalling GRUB to partition, not disk
- editing `fstab` blindly
- rebooting without testing `mount -a`

---

## Mental Model

If you can:
- mount disks
- bind `/dev /proc /sys`
- chroot

You can recover **almost any Linux system**.

---

## Docs

```bash
man chroot
man mount
man grub-install
man efibootmgr
```

