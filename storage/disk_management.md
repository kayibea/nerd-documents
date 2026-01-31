# Bash/Linux Disk & Partition Management Cheat Sheet

## 1. Disk Information

| Command | Description | Tricks |
|---------|------------|--------|
| `lsblk` | List block devices and partitions | `lsblk -f` (show filesystem types), `lsblk -o NAME,SIZE,TYPE,MOUNTPOINT,FSTYPE` for custom columns |
| `blkid` | Show block device IDs, UUIDs, types | `blkid /dev/sda1` |
| `df` | Disk space usage | `df -h` (human-readable), `df -T` (include filesystem type) |
| `du` | Directory size usage | `du -sh /var/log` (summarize folder size) |
| `fdisk -l` | List partition table | Shows partitions, sizes, types, boot flags |
| `parted -l` | List partitions with details | `parted /dev/sda print` |
| `mount` | Show mounted filesystems | `mount | column -t` for readability |
| `ls -l /dev/disk/by-uuid/` | Show UUID links | Useful for fstab references |

## 2. Partitioning

| Command | Description | Tricks |
|---------|------------|--------|
| `fdisk /dev/sdx` | Interactive MBR partitioning | Use `m` for help inside fdisk, `n` to create, `d` to delete, `w` to write |
| `parted /dev/sdx` | GPT/MBR partitioning | `mklabel gpt` (create GPT table), `mkpart primary ext4 1MiB 100%`, `set 1 boot on` |
| `cfdisk /dev/sdx` | ncurses-based partitioning | Visual interface, easier for beginners |
| `lsblk` | Verify new partitions | Check new sizes and types after partitioning |

## 3. Filesystem Management

| Command | Description | Tricks |
|---------|------------|--------|
| `mkfs` | Create filesystem | `mkfs.ext4 /dev/sda1`, `mkfs.xfs /dev/sdb1` |
| `mkfs -t ext4 /dev/sda1` | Specify type explicitly | Same as above, more explicit syntax |
| `fsck` | Check and repair filesystem | `fsck -y /dev/sda1` (auto-fix), `fsck.ext4 -f /dev/sda1` |
| `tune2fs` | Modify ext2/3/4 filesystem parameters | `tune2fs -L 'DATA' /dev/sda1` (label), `tune2fs -i 1w /dev/sda1` (check interval) |
| `resize2fs` | Resize ext2/3/4 filesystems | `resize2fs /dev/sda1 20G` |
| `xfs_growfs` | Resize XFS filesystem | Must be mounted, `xfs_growfs /mountpoint`

## 4. Mounting / Unmounting

| Command | Description | Tricks |
|---------|------------|--------|
| `mount /dev/sda1 /mnt` | Mount a device | Can specify filesystem: `mount -t ext4 /dev/sda1 /mnt` |
| `umount /mnt` | Unmount filesystem | `umount -l /mnt` (lazy unmount) |
| `mount -o remount,rw /` | Remount root as read/write | Useful for live environments |
| `fstab` | Persistent mounts | `/etc/fstab` configuration, use UUID for stability |
| `findmnt` | Show mount points | `findmnt /mnt` or `findmnt -t ext4`

## 5. Disk Usage & Quotas

| Command | Description | Tricks |
|---------|------------|--------|
| `du -sh *` | Summary sizes of files/folders | `du -sh /var/*` for quick overview |
| `df -h` | Check free space | `df -hT` (include filesystem type) |
| `quota -v username` | Show user quotas | Requires quota setup on filesystem |
| `repquota /` | Report quotas on filesystem | Useful for admin overview |
| `ncdu /` | Interactive disk usage viewer | `apt install ncdu`, navigate interactively |

## 6. Advanced Disk Tools

| Command | Description | Tricks |
|---------|------------|--------|
| `lsblk -o +UUID` | Show partitions with UUID | Useful for scripting and fstab references |
| `wipefs` | Erase filesystem signatures | `wipefs -a /dev/sdx` to remove all traces |
| `dd` | Low-level copy | `dd if=/dev/sda of=/dev/sdb bs=64K conv=noerror,sync` (clone disk), `dd if=/dev/zero of=/dev/sdx bs=1M count=100` (wipe) |
| `smartctl` | Disk health monitoring | `smartctl -a /dev/sda` (full SMART report) |
| `hdparm` | Disk info and performance | `hdparm -I /dev/sda`, `hdparm -t /dev/sda` (benchmark)

## 7. LVM (Logical Volume Management)

| Command | Description | Tricks |
|---------|------------|--------|
| `pvcreate /dev/sdx` | Initialize physical volume | Prepare for volume group creation |
| `vgcreate myvg /dev/sdx` | Create volume group | `vgdisplay myvg` to inspect |
| `lvcreate -n mylv -L 20G myvg` | Create logical volume | `lvdisplay /dev/myvg/mylv` to inspect |
| `lvextend -L +10G /dev/myvg/mylv` | Resize LV | Resize filesystem after (e.g., `resize2fs`) |
| `lvremove /dev/myvg/mylv` | Remove logical volume | Be careful, destroys data |
| `vgextend` / `vgreduce` | Add/remove PVs from VG | Flexible LVM management |
| `pvdisplay` / `vgdisplay` / `lvdisplay` | Inspect LVM structures | Always check before changes

## 8. Tips & Tricks

- Always **backup data** before partitioning or filesystem operations.
- Use `lsblk` and `blkid` to double-check devices.
- Use **UUIDs** in `/etc/fstab` for stable mounts.
- For large wipes or clones, `dd` with `status=progress` is useful.
- Use `ncdu` for an interactive way to analyze disk usage.
- `smartctl` is critical for checking disk health before relying on drives.
- For LVM, always check `vgdisplay` and `lvdisplay` before resizing or removing volumes.
- When mounting, `mount -o ro` is safer for read-only checks.
- Keep a consistent naming convention for LVs, VGs, and mount points for clarity.