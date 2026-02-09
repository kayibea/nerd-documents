# Home Server Storage & Backup Documentation

This document fully describes the storage, mounting, RAID, and backup design for a simple, reliable Linux home server. It is written to be readable months or years later, even if you forget the original reasoning.

---

## Goals

* Simple, boring, reliable setup
* No NAS abstractions or vendor lock-in
* Transparent filesystems (ext4 + rsync)
* Protection against:

  * accidental deletion
  * bad updates
  * disk failure
* Easy recovery and understanding

---

## Server Overview

### System Disk (SSD – 1TB)

Partition layout:

```
/boot/efi   250M
/swap       2–4G (or swapfile)
/home       50G (admin user only)
/           remainder
```

Notes:

* `/srv` lives under `/` and holds all service data
* No separate partition for `/srv` (intentional)
* Root filesystem must have enough free space for services

---

## Data Layout Philosophy

* System and services live on the SSD
* Backups live on HDDs
* Backup disks are **never** used for live services
* RAID (if used) lives **below** the filesystem

---

## Backup Disks

### Hardware

* 2 × 2TB HDD
* Used only for backups
* Internal SATA (preferred) or USB

---

## Mount Points Explained

### What is a mount point?

A mount point is just a directory. When a filesystem is mounted there, that directory becomes a *view* into another filesystem.

Example:

```
/mnt/backup
```

* Before mounting: empty directory on SSD
* After mounting: entry point to HDD or RAID filesystem

Nothing written to a mounted directory uses SSD space.

---

## RAID Design (Optional but Recommended)

### RAID Type

* RAID1 using `mdadm`
* Purpose: disk failure tolerance
* Not a backup by itself

### RAID Stack

```
/dev/sda   HDD1
/dev/sdb   HDD2
   \       /
    mdadm RAID1
      ↓
   /dev/md0   (virtual disk)
      ↓
   ext4 filesystem
      ↓
   /mnt/backup
```

### Key Properties

* `/dev/md0` behaves like a normal disk
* Writes are mirrored automatically
* One disk can fail without data loss
* Rebuild happens automatically after replacement

---

## Why `/mnt/hdd1` and `/mnt/hdd2` Are Not Mounted

When disks are RAID members:

* They must **not** be mounted individually
* Mounting them bypasses RAID and causes corruption
* Only the RAID device is mounted

Correct:

```
/mnt/backup   ← mounted
```

Incorrect:

```
/mnt/hdd1     ← ❌
/mnt/hdd2     ← ❌
```

---

## Backup Strategy

### Tooling

* Timeshift (rsync mode)
* ext4 filesystem
* Hard-link based snapshots

### What Is Backed Up

* `/srv`
* `/etc`
* `/home` (optional)

Excluded:

* `/proc`
* `/sys`
* `/dev`
* `/tmp`
* caches

---

## Backup Retention Policy

Recommended:

* 24 hourly
* 7 daily
* 4 weekly
* 2–3 monthly

Purpose:

* Hourly: accidental deletes
* Daily: bad updates
* Weekly: slow mistakes
* Monthly: long-term recovery

---

## Where Backups Are Written

Timeshift target:

```
/mnt/backup
```

Reality:

* Data is written directly to HDDs or RAID
* SSD usage is unaffected
* Root filesystem fullness does not matter

---

## Critical Safety Rule

> `/mnt/backup` must be mounted before backups run.

If it is not mounted:

* it becomes a normal directory on `/`
* backups will fill the SSD

---

## Mount Safety (fstab example)

```
/dev/md0  /mnt/backup  ext4  nofail,x-systemd.automount  0  2
```

This:

* prevents boot failure
* mounts on first access
* reduces accidental writes to SSD

---

## Verifying the Setup

Run:

```bash
df -h
```

Expected output:

```
/dev/md0   1.8T  200G  1.6T  /mnt/backup
```

If you see `/dev/sda` or `/dev/sdb`, RAID is not mounted correctly.

---

## Database Backups (Important)

For databases (Postgres, MySQL, etc.):

* Dump databases to files:

  * `/srv/backups/db/`
* Let Timeshift back up the dumps

Never rsync live database files.

---

## What RAID Protects vs Backups

RAID protects against:

* disk failure

Backups protect against:

* accidental deletion
* corruption
* bad updates
* user error

You need both for a reliable system.

---

## Optional Extra Safety

* Secondary offline HDD
* Periodic rsync to unplugged disk
* Test restores every few months

---

## Mental Model Summary

* Disks = hardware
* RAID = virtual disk
* Filesystem = structure
* Mount point = access location
* Timeshift = versioned copies

You only ever interact with mount points.

---

## Final Notes

This setup is intentionally boring.

That is a feature.

Boring servers survive upgrades, mistakes, and time.
