# Backup & Recovery Cheat Sheet

## 1. Core Concepts
- **Backup** = copy data elsewhere.
- **Recovery** = restoring that data.
- **Snapshot** = point-in-time state (fast, efficient).
- **Image** = full cloned disk/partition.

---

## 2. rsync (Incremental Backups)

### Common Commands
| Command | Description |
|---------|-------------|
| `rsync -av src/ dest/` | Basic archive sync |
| `rsync -avh --progress src/ dest/` | Human-readable + progress |
| `rsync -av --delete src/ dest/` | Mirror; delete removed files |
| `rsync -az src/ user@host:/backup/` | Backup over SSH (compressed) |

### Exclusions
```
rsync -av --exclude='node_modules' --exclude='*.mp4' src/ dest/
```

---

## 3. tar (Archives)
Use for packaging backups.

### Create
```
tar -czf backup.tar.gz /home/user
```
### Extract
```
tar -xzf backup.tar.gz -C /restore/path
```

---

## 4. dd (Disk/Partition Images)
Low-level cloning.

### Create disk image
```
sudo dd if=/dev/sda of=/mnt/backup/sda.img bs=64M status=progress
```

### Restore disk image
```
sudo dd if=sda.img of=/dev/sda bs=64M status=progress
```

### Clone disk → disk
```
sudo dd if=/dev/sda of=/dev/sdb bs=64M status=progress
```

⚠ dd overwrites blindly. One typo = total data loss.

---

## 5. Partclone (Efficient Disk Imaging)
Much faster and smaller than dd.

### Backup
```
sudo partclone.ext4 -c -s /dev/sda1 -o sda1.img
```
### Restore
```
sudo partclone.ext4 -r -s sda1.img -o /dev/sda1
```

---

## 6. Clonezilla (Full-System Imaging)
Interactive tool. Works with whole systems/disks.

- Supports ext, btrfs, xfs, NTFS, FAT.
- Run from USB.

Modes:
- device → image
- image → device
- device → device

---

## 7. Timeshift (System Snapshots)
Perfect for desktop rollback (like Windows restore points).
Supports **rsync mode** and **Btrfs snapshot mode**.

### Key Features
- Auto snapshots (hourly, daily, etc.)
- Restore system without touching user files
- Great for Debian systems using Btrfs

### CLI Usage
```
sudo timeshift --list
sudo timeshift --create --comments "before update"
sudo timeshift --restore --snapshot "2025-01-01_12-00-00"
```

---

## 8. btrfs Snapshots (Native Snapshots)
Instant, space-efficient.

### Create snapshot
```
sudo btrfs subvolume snapshot / /@snapshot
```
### Read-only snapshot
```
sudo btrfs subvolume snapshot -r / /@snap_ro
```

### Delete
```
sudo btrfs subvolume delete /@snapshot
```

---

## 9. LVM Snapshots
For ext4/XFS setups using LVM.

### Create snapshot
```
sudo lvcreate --size 2G --snapshot --name rootsnap /dev/vg0/root
```
### Restore
```
sudo lvconvert --merge /dev/vg0/rootsnap
```

---

## 10. borg / borgmatic (Deduplicated Backups)
Modern, encrypted, deduping.

### Initialize repo
```
borg init --encryption=repokey /mnt/backup/borg_repo
```
### Backup
```
borg create /mnt/backup/borg_repo::mybackup-1 /home/user
```
### List
```
borg list /mnt/backup/borg_repo
```
### Extract
```
borg extract /mnt/backup/borg_repo::mybackup-1
```

---

## 11. Timeshift vs Btrfs Snapshots vs rsync
| Tool | Use Case |
|------|----------|
| **Timeshift** | Simple system rollback |
| **btrfs snapshots** | Super-fast, local, boot-integrated |
| **rsync** | File-level backups to remote/local |
| **dd/partclone** | Disk-level clones |
| **borg** | Deduplicated, encrypted archives |

---

## 12. Recovery Techniques

### Restore from tar
```
tar -xzf backup.tar.gz -C /
```

### Restore system via Timeshift
Boot into recovery → run Timeshift GUI/CLI.

### Restore disk via dd
```
dd if=disk.img of=/dev/sda bs=64M status=progress
```

### Restore Btrfs snapshot
```
sudo btrfs subvolume delete /@
sudo btrfs subvolume snapshot /@snapshot /
```

---

## 13. Disaster Prevention Tips
- Never store backups on same disk.
- Always test restoring.
- Keep at least 3 backups: **local**, **external**, **remote**.
- Automate + rotate old backups.
- Encrypt remote/off-site backups.

---

## 14. Useful Tools Summary
- **rsync** → Quick, incremental, remote sync
- **Timeshift** → Desktop rollback
- **btrfs** → Native snapshots
- **partclone/Clonezilla** → Complete system imaging
- **dd** → Bit-level cloning
- **borg** → Encrypted + dedup + efficient

