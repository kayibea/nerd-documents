# Recovery & Forensics Cheatsheet

---

## Scope
Tools for **system recovery and file forensics**:
- `dd` — low-level disk copy
- `losetup` — loop devices
- `testdisk` — partition recovery
- `photorec` — file carving
- `foremost` — file carving / forensic recovery

Useful when disks fail, partitions are lost, or files deleted.

---

## dd — Disk Copy / Imaging

### Basic Syntax
```bash
dd if=/dev/sdX of=/path/to/image.img bs=4M status=progress
```
- `if=` → input file/device
- `of=` → output file/device
- `bs=` → block size
- `status=progress` → live progress

### Clone Entire Disk
```bash
dd if=/dev/sda of=/dev/sdb bs=64K conv=noerror,sync status=progress
```
- `conv=noerror,sync` → continue on read errors, pad blocks

### Create Compressed Image
```bash
dd if=/dev/sda bs=4M conv=noerror,sync | gzip > /mnt/backup/disk.img.gz
```

**Rule of thumb**: Always double-check `if` and `of` — swapping them can destroy your disk.

---

## losetup — Loop Devices

### Associate a File as a Device
```bash
sudo losetup /dev/loop0 disk.img
```

### View Loop Devices
```bash
losetup -a
```

### Detach Loop Device
```bash
sudo losetup -d /dev/loop0
```

Useful for mounting disk images or forensic analysis without writing to the disk.

---

## testdisk — Partition Recovery

### Start Recovery
```bash
sudo testdisk /dev/sdX
```

### Common Use Cases
- Recover deleted partitions
- Restore boot sectors
- List filesystem structures

Interactive; follow on-screen instructions. Select **Write** carefully.

---

## photorec — File Carving

### Recover Deleted Files by Signature
```bash
sudo photorec /log /dev/sdX
```

- Does not care about filesystem; scans raw data
- Works with FAT, NTFS, ext, exFAT, HFS+, etc.
- Output goes to user-selected directory

### Notes
- Safe: only reads source disk
- Slow on large disks
- Can recover thousands of file types

---

## foremost — File Carving / Forensics

### Recover Specific File Types
```bash
sudo foremost -t jpg,pdf,doc -i /dev/sdX -o /mnt/recovery
```

- `-t` → file types (jpg, pdf, doc, etc.)
- `-i` → input device/image
- `-o` → output directory

### Useful Flags
- `-v` → verbose
- `-c 4` → 4 concurrent threads (speedup)

Foremost works well for selective recovery.

---

## Recommended Workflow

1. **Disk Imaging First** (dd) → never work on live disk
2. **Loop Device Setup** (losetup) → mount or scan safely
3. **Partition Recovery** (testdisk) → rebuild table if needed
4. **File Recovery** (photorec / foremost) → carve data from image
5. **Validation** → checksum, compare recovered data

**Rule**: Always make a raw image copy first — it’s your safety net.

---

## References / Docs
```bash
man dd
man losetup
man testdisk
man photorec
man foremost
```

