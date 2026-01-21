# fstab & Filesystem Mounting Cheat Sheet

## 1. Purpose
`/etc/fstab` defines filesystems to mount automatically at boot or manually.

- Each line represents a filesystem and its options.
- Commonly used for disks, partitions, network mounts, tmpfs, and swap.

## 2. Format
```
<device> <mount_point> <fs_type> <options> <dump> <pass>
```
- **device**: Device file, UUID, or LABEL (e.g., `/dev/sda1`, `UUID=xxxx`, `LABEL=Data`).
- **mount_point**: Where the filesystem is mounted (e.g., `/`, `/home`, `/mnt/data`).
- **fs_type**: ext4, xfs, btrfs, swap, tmpfs, nfs, cifs, etc.
- **options**: Comma-separated mount options (see below).
- **dump**: `0` or `1`, used by `dump` utility for backups.
- **pass**: `0` = no fsck, `1` = root fs, `2` = other fs.

## 3. Common Mount Options
| Option | Description |
|--------|-------------|
| `defaults` | rw, suid, dev, exec, auto, nouser, async |
| `ro` | Read-only |
| `rw` | Read-write |
| `noexec` | Do not allow execution |
| `nosuid` | Ignore SUID/SGID bits |
| `nodev` | Ignore device files |
| `auto` | Mount at boot |
| `noauto` | Do not mount at boot |
| `user` | Allow normal user to mount |
| `nouser` | Only root can mount |
| `sync` | Writes are synchronous |
| `async` | Writes are asynchronous |
| `relatime` | Update atime only if older than mtime/ctime |
| `bind` | Bind mount another path |

## 4. Using UUIDs and Labels
```
blkid                 # list UUIDs and labels
UUID=xxxx-xxxx /mnt/data ext4 defaults 0 2
LABEL=Backup /mnt/backup ext4 defaults 0 2
```

## 5. Network Filesystems
| FS Type | Options |
|---------|---------|
| `nfs`  | `rw,sync,hard,intr` |
| `cifs` | `username=user,password=pass,iocharset=utf8,vers=3.0` |

Example:
```
server:/export /mnt/nfs nfs defaults 0 0
//server/share /mnt/cifs cifs username=user,password=pass,uid=1000,gid=1000 0 0
```

## 6. Swap
```
/dev/sda2 none swap sw 0 0
```
- Use `swapon -s` to list active swap.
- Use `mkswap /dev/sda2` to prepare swap partition.

## 7. tmpfs (RAM-based filesystem)
```
tmpfs /tmp tmpfs defaults,noatime,mode=1777 0 0
```
- Great for temp directories or caching.

## 8. Mounting & Testing
```
mount -a       # Mount everything in fstab
mount /mnt/data # Mount specific entry
umount /mnt/data
```

### Check fstab syntax
```
findmnt --verify
systemd-analyze verify /etc/fstab
```

## 9. Tips & Best Practices
- Use UUID or LABEL to avoid device renaming issues.
- Always backup `/etc/fstab` before editing.
- Test entries with `mount -a` before reboot.
- Use `noauto` for removable drives.
- For network mounts, consider `x-systemd.automount` for lazy mounting.
- Document any special options in comments for clarity.

