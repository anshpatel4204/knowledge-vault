## 1. Introduction

Linux manages storage through a layered system of physical disks, partitions, filesystems, and mount points — all unified into the single filesystem tree described in [[Linux_File_System_Hierarchy]]. This note covers the practical commands and concepts for viewing, partitioning, formatting, and mounting storage.

## 2. Viewing Disk Usage

```bash
df -h                  # disk space usage per mounted filesystem, human-readable
df -i                     # inode usage (a filesystem can run out of inodes even with free space!)
du -sh /path/to/dir          # total size of a directory, summarized
du -h --max-depth=1 /var       # size of each subdirectory one level deep
ncdu /path                       # interactive, navigable disk usage explorer (may need installing)
```

**df vs du:** `df` reports filesystem-level free/used space; `du` reports the actual size of specific files/directories — they can appear to disagree if a file is deleted but still held open by a running process (see [[File_Types_and_Inodes]]).

## 3. Viewing Block Devices and Partitions

```bash
lsblk                  # tree view of block devices and their partitions/mount points
lsblk -f                  # include filesystem type and UUID
fdisk -l                    # list all disks and partitions (requires root for full detail)
blkid                         # show UUID and filesystem type per block device
```

## 4. Partitioning Tools

| Tool | Notes |
|---|---|
| `fdisk` | Classic, MBR-focused (supports GPT with modern versions), interactive menu-driven |
| `gdisk` | GPT-focused equivalent of fdisk |
| `parted` | Supports both MBR and GPT, can be used interactively or scripted |
| `cfdisk` | Simpler ncurses-based interactive partitioning tool |

```bash
sudo fdisk /dev/sdb          # enter interactive partitioning mode on a disk
# Inside fdisk: n (new partition), p (primary), w (write changes and exit)

sudo parted /dev/sdb print      # show partition table
```

**Warning:** partitioning tools operate directly on raw disks — targeting the wrong device (`/dev/sda` instead of `/dev/sdb`) can destroy the operating system's own disk. Always double-check the target device.

## 5. Creating Filesystems

```bash
sudo mkfs.ext4 /dev/sdb1        # format a partition with ext4
sudo mkfs.xfs /dev/sdb1            # format with XFS
sudo mkfs.vfat /dev/sdb1              # format with FAT32 (common for USB/cross-platform compatibility)
```

## 6. Common Linux Filesystem Types

| Filesystem | Notes |
|---|---|
| ext4 | The long-standing default on most distros — mature, stable, journaling |
| XFS | High-performance, scales well for large files, default on RHEL/CentOS |
| Btrfs | Modern, supports snapshots, built-in RAID-like features, copy-on-write |
| ZFS | Advanced (originally Solaris), snapshots, checksumming, pooled storage — less commonly default due to licensing complications on Linux |
| FAT32/exFAT | Cross-platform compatibility (USB drives), lacks Linux permissions/journaling |
| NTFS | Windows-native, readable/writable on Linux via `ntfs-3g` driver |

## 7. Mounting and Unmounting

```bash
sudo mount /dev/sdb1 /mnt/data          # mount a partition to a directory
sudo umount /mnt/data                      # unmount (note: "umount," not "unmount")
sudo mount -o remount,ro /                    # remount with different options (e.g., read-only)
```

Mounting attaches a filesystem's contents into a specific point in the unified directory tree (see [[Linux_File_System_Hierarchy]]) — until mounted, the device's contents aren't accessible through the filesystem at all.

## 8. /etc/fstab — Persistent Mount Configuration

Defines filesystems that should be mounted automatically at boot.

```
# device                                mount point  fs-type  options        dump  pass
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  /            ext4     defaults       0     1
/dev/sdb1                                  /mnt/data    ext4     defaults,noatime  0  2
```

| Field | Meaning |
|---|---|
| Device | Identified by device path, UUID (preferred, stable across reboots/reordering), or label |
| Mount point | Where in the tree it's attached |
| Filesystem type | ext4, xfs, etc. |
| Options | `defaults`, `ro` (read-only), `noatime` (skip access-time updates for performance), `noexec` (prevent execution of binaries — a hardening option for `/tmp`), `nosuid` (ignore SUID bits — another hardening option) |
| Dump | Legacy backup flag (usually 0) |
| Pass | fsck check order at boot (0 = skip, 1 = root filesystem first, 2 = checked after) |

```bash
sudo mount -a          # mount everything listed in fstab (useful to test for errors before rebooting)
```

**Security-relevant fstab options:** mounting `/tmp` with `noexec,nosuid,nodev` is a common hardening step, preventing that world-writable directory from being used to execute planted binaries.

## 9. LVM (Logical Volume Manager)

LVM adds a flexible abstraction layer between physical disks and filesystems, allowing storage to be resized, combined, and snapshotted without being locked to fixed physical partition boundaries.

```
Physical Volume (PV) → Volume Group (VG) → Logical Volume (LV) → Filesystem
```

```bash
pvcreate /dev/sdb1          # initialize a physical volume
vgcreate myvg /dev/sdb1        # create a volume group from one or more PVs
lvcreate -L 10G -n mylv myvg      # create a 10GB logical volume within the group
mkfs.ext4 /dev/myvg/mylv            # format the logical volume like any normal partition

lvextend -L +5G /dev/myvg/mylv        # grow a logical volume by 5GB
resize2fs /dev/myvg/mylv                 # grow the ext4 filesystem to match (xfs_growfs for XFS)

pvs; vgs; lvs                              # list physical volumes, volume groups, logical volumes
```

**Why LVM matters:** allows resizing storage without downtime/repartitioning, supports snapshots (useful for consistent backups), and can span multiple physical disks into one logical pool.

## 10. Swap Space

Swap acts as overflow for RAM, allowing the kernel to move inactive memory pages to disk when physical RAM is exhausted.

```bash
free -h                     # view RAM and swap usage
swapon --show                  # show active swap devices
sudo mkswap /dev/sdb2             # format a partition as swap
sudo swapon /dev/sdb2                # activate it

# Or a swap file instead of a dedicated partition
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

## 11. Checking and Repairing Filesystems

```bash
sudo fsck /dev/sdb1              # check (and optionally repair) a filesystem — should NOT be run on a mounted filesystem
sudo fsck -y /dev/sdb1              # automatically answer "yes" to repair prompts
```

## 12. Interview Questions

1. What's the difference between `df` and `du`? → **`df` reports filesystem-level free/used space; `du` reports the actual disk usage of specific files/directories**
2. Why might `df` and `du` disagree about how much space is used? → **A deleted file still held open by a running process still occupies space that `df` counts but `du` won't see, since it's no longer listed in the directory**
3. What does `/etc/fstab` control? → **Which filesystems get mounted automatically at boot, and with what options**
4. Why is using a UUID preferable to a device path like `/dev/sdb1` in fstab? → **Device paths can change/reorder across reboots (e.g., if a disk is added/removed); UUIDs remain stable**
5. What does LVM allow that traditional fixed partitioning doesn't? → **Resizing volumes, spanning multiple physical disks, and taking snapshots without being locked to fixed physical partition boundaries**
6. Why would you mount `/tmp` with `noexec,nosuid`? → **Security hardening — prevents that world-writable directory from being used to execute planted malicious binaries or leverage SUID abuse**

## 13. Key Points

- **`df`** shows filesystem-level space; **`du`** shows actual file/directory sizes; **`lsblk`**/**`fdisk -l`** show block devices and partitions.
- Common filesystems: **ext4** (traditional default), **XFS** (RHEL default, high-performance), **Btrfs/ZFS** (advanced, snapshot-capable).
- **`mount`**/**`umount`** attach/detach filesystems; **`/etc/fstab`** makes mounts persistent across reboots.
- **LVM** adds a flexible abstraction layer (PV → VG → LV) enabling resizing and snapshots beyond fixed partitions.
- Security-hardening mount options (`noexec`, `nosuid`, `nodev`) on directories like `/tmp` are a standard hardening practice.

---
*Related: [[Linux_File_System_Hierarchy]], [[File_Types_and_Inodes]], [[Linux_Security_Hardening]]*
