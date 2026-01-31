# Compression & Archiving Cheat Sheet

## 1. tar (Archive Tool)
Tar creates archives; compression is added via flags.

### Common Commands
| Command | Description |
|---------|-------------|
| `tar -cf file.tar dir/` | Create archive |
| `tar -xf file.tar` | Extract archive |
| `tar -tvf file.tar` | List contents |
| `tar -czf file.tar.gz dir/` | Create gzip-compressed archive |
| `tar -xzf file.tar.gz` | Extract gzip archive |
| `tar -cjf file.tar.bz2 dir/` | Create bzip2 archive |
| `tar -xjf file.tar.bz2` | Extract bzip2 archive |
| `tar -caf file.tar.xz dir/` | Create xz archive |
| `tar -xaf file.tar.xz` | Extract xz archive |

### Useful Tricks
- Exclude files: `tar -cf file.tar dir/ --exclude='*.log'`
- Extract to specific directory: `tar -xf file.tar -C /path/`

---

## 2. gzip / gunzip
Fast compression, widely supported.

| Command | Description |
|---------|-------------|
| `gzip file` | Compress file → `file.gz` |
| `gunzip file.gz` | Decompress |
| `gzip -k file` | Keep original file |
| `gzip -d file.gz` | Same as gunzip |

---

## 3. bzip2 / bunzip2
Better compression than gzip, slower.

| Command | Description |
|---------|-------------|
| `bzip2 file` | Compress |
| `bunzip2 file.bz2` | Decompress |

---

## 4. xz
High compression ratio, slow.

| Command | Description |
|---------|-------------|
| `xz file` | Compress |
| `xz -d file.xz` | Decompress |
| `xz -k file` | Keep original |

---

## 5. zip / unzip
Cross-platform compression.

### Commands
| Command | Description |
|---------|-------------|
| `zip file.zip file1 file2` | Create ZIP archive |
| `zip -r file.zip dir/` | Zip directory |
| `unzip file.zip` | Extract |
| `unzip -l file.zip` | List |

---

## 6. 7z (p7zip)
Versatile, supports many formats.

| Command | Description |
|---------|-------------|
| `7z a archive.7z dir/` | Create 7z archive |
| `7z x archive.7z` | Extract |
| `7z l archive.7z` | List contents |

---

## 7. rar / unrar
For legacy archives.

| Command | Description |
|---------|-------------|
| `rar a file.rar dir/` | Create RAR |
| `unrar x file.rar` | Extract |

---

## 8. split & join (chunking files)

| Command | Description |
|---------|-------------|
| `split -b 100M big.iso part_` | Split into 100MB chunks |
| `cat part_* > big.iso` | Reassemble |

---

## 9. rsync (Backup/Sync Tool with Compression)

| Command | Description |
|---------|-------------|
| `rsync -avz src/ dest/` | Compress during transfer |
| `rsync -a --progress` | Show progress |

---

## 10. zstd (Modern High-Speed Compression)
Fast and efficient.

| Command | Description |
|---------|-------------|
| `zstd file` | Compress → `file.zst` |
| `unzstd file.zst` | Decompress |
| `zstd -T0 file` | Use all threads |

---

## 11. Best Use-Cases Summary
- **gzip** → Speed
- **bzip2** → Good ratio, slower
- **xz** → Highest ratio, very slow
- **zstd** → Best modern balance
- **zip** → Cross-platform
- **tar** → Archiving directories
- **7z** → Max compression on mixed data

---

## 12. Tricks & Tips
- Verify archives: `tar -tf file.tar.gz` before extracting.
- Use `pv` to show progress: `pv bigfile | gzip > bigfile.gz`.
- Combine with SSH: `tar -cz dir | ssh host 'tar -xz -C /dest'`.
- Avoid compressing already compressed files (mp4, jpg, zip).