# Disk Encryption Cheatsheet — LUKS (cryptsetup) & Veracrypt

---

## 1. cryptsetup + LUKS (Native Linux)

### Format a Partition for Encryption
```bash
sudo cryptsetup luksFormat /dev/sdXn
```
- WARNING: This erases all data on the partition.
- Follow prompts to set passphrase.

### Open Encrypted Volume
```bash
sudo cryptsetup luksOpen /dev/sdXn myencrypted
```
- Creates `/dev/mapper/myencrypted`

### Mount Decrypted Volume
```bash
sudo mount /dev/mapper/myencrypted /mnt
```

### Close Encrypted Volume
```bash
sudo umount /mnt
sudo cryptsetup luksClose myencrypted
```

### Add / Remove Key
```bash
sudo cryptsetup luksAddKey /dev/sdXn
sudo cryptsetup luksRemoveKey /dev/sdXn
```

### Automatic Unlock on Boot
- Add entry to `/etc/crypttab`:
```text
myencrypted /dev/sdXn none luks
```
- Update initramfs:
```bash
sudo update-initramfs -u
```

### Status / Info
```bash
sudo cryptsetup luksDump /dev/sdXn
```

---

## 2. Veracrypt (Cross-Platform)

### Create Encrypted Volume (Container)
- GUI: Create a new volume → Standard or Hidden → Set size, password, encryption
- CLI example:
```bash
veracrypt --text --create /path/to/container.vc --size 1G --encryption AES --hash SHA-512 --filesystem ext4
```

### Mount Encrypted Volume
```bash
veracrypt /path/to/container.vc /mnt/encrypted
```
- GUI: select container, choose mount point, enter password

### Dismount Volume
```bash
veracrypt -d /mnt/encrypted
```
- GUI: select mounted volume → Dismount

### Encrypt Entire Partition / Drive
```bash
veracrypt --text --encrypt /dev/sdX
```
- Follow prompts carefully, backup first!

### Key Features
- Cross-platform compatibility
- Supports containers, partitions, and full-disk encryption
- Hidden volumes for plausible deniability
- GUI and CLI available

---

## Comparison
| Feature                     | LUKS (cryptsetup)        | Veracrypt                       |
|-------------------------------|------------------------|---------------------------------|
| Platform                     | Linux only             | Linux, Windows, macOS           |
| Container support            | No                     | Yes                             |
| Full-disk encryption         | Yes                    | Yes                             |
| Boot integration             | Yes (initramfs)        | Limited                         |
| Hidden volumes               | No                     | Yes                             |
| Key management               | Passphrase / keyfile   | Passphrase / keyfile            |
| GUI                          | Rare / third-party      | Official                        |

---

## References / Docs
```bash
man cryptsetup
cryptsetup --help
veracrypt --text --help
```

