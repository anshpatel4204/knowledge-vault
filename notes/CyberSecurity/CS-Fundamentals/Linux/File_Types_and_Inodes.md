## 1. Introduction

True to the Unix philosophy "everything is a file" (see [[Linux_Basics]]), Linux represents far more than just documents and programs as files — devices, processes, network sockets, and inter-process communication channels are all accessed through the filesystem interface. Understanding file types and the underlying **inode** structure is essential for both system administration and forensic investigation.

## 2. The Seven File Types

Linux recognizes seven distinct file types, visible as the first character in `ls -l` output:

| Symbol | Type | Description |
|---|---|---|
| `-` | Regular file | Ordinary data — text, binaries, images, archives |
| `d` | Directory | A special file containing a list of other files/directories |
| `l` | Symbolic (soft) link | A pointer/reference to another file's path |
| `c` | Character device | Devices handling data as a continuous stream (keyboard, serial port) |
| `b` | Block device | Devices handling data in fixed-size blocks (hard disks, USB drives) |
| `s` | Socket | Endpoint for inter-process communication over a network-like interface |
| `p` | Named pipe (FIFO) | A conduit for inter-process communication, data flows in one direction |

```bash
ls -l /
drwxr-xr-x  ...  etc      ← directory (d)
-rw-r--r--  ...  file.txt ← regular file (-)
lrwxrwxrwx  ...  link     ← symbolic link (l)
brw-rw----  ...  sda      ← block device (b)
crw-rw-rw-  ...  null     ← character device (c)
```

## 3. Regular Files

The most common type — holds actual data. Linux doesn't use file extensions to determine file type the way Windows does; the `file` command inspects the actual content/magic bytes to determine what a file really is, regardless of its name.

```bash
file document.txt
file suspicious_binary    # useful in malware/forensic triage — extension can lie
```

## 4. Directories

A directory is technically a special file containing a table mapping filenames to **inode numbers** — it doesn't store the actual file data itself, just these name-to-inode associations.

## 5. Device Files (`/dev`)

Represent hardware devices as files, allowing programs to interact with hardware using standard file I/O operations (`read`/`write`) instead of hardware-specific code.

| Device File | Represents |
|---|---|
| `/dev/sda`, `/dev/nvme0n1` | Block devices (whole disks) |
| `/dev/sda1` | A partition on a block device |
| `/dev/null` | Discards all data written to it — a "black hole" (see [[Piping_Redirection_and_Operators]]) |
| `/dev/zero` | Produces an infinite stream of null bytes |
| `/dev/random`, `/dev/urandom` | Sources of random data, used by cryptographic operations |
| `/dev/tty` | The current terminal |

## 6. Sockets and Named Pipes (FIFOs)

Both enable **inter-process communication (IPC)**:

| Type | Description |
|---|---|
| Named Pipe (FIFO) | A one-way communication channel with a filesystem path — one process writes, another reads, in the order written (First In, First Out) |
| Socket | A bidirectional communication endpoint, commonly used for local IPC (Unix domain sockets, e.g., `/var/run/docker.sock`) or network communication |

```bash
mkfifo mypipe            # create a named pipe
```

## 7. Inodes — The Core Data Structure

An **inode (index node)** is a data structure storing all metadata about a file **except its name and actual data content**:

| Stored in the Inode | NOT Stored in the Inode |
|---|---|
| File type, permissions | Filename |
| Owner (UID) and group (GID) | — |
| File size | — |
| Timestamps (atime, mtime, ctime) | — |
| Number of hard links | — |
| Pointers to the actual data blocks on disk | — |

The **filename lives in the containing directory**, which simply maps a name to an inode number — this indirection is exactly what makes hard links possible (see below).

```bash
ls -i file.txt          # show a file's inode number
stat file.txt            # show full inode metadata
```

## 8. The Three Timestamps

| Timestamp | Meaning | Updated When |
|---|---|---|
| **mtime** (modification time) | Last time file *content* changed | File data is written |
| **ctime** (change time) | Last time file *metadata* (permissions, owner, etc.) changed | chmod, chown, or content change |
| **atime** (access time) | Last time file was *read* | File is opened/read (often disabled via `noatime` mount option for performance) |

**Forensic relevance:** the **MAC times** (Modified, Accessed, Changed — note: not "created," Linux traditionally has no creation timestamp field, though some modern filesystems like ext4 add `crtime`) are heavily analyzed in digital forensics to reconstruct a timeline of file/system activity — and can be deliberately manipulated by attackers ("timestomping") to hide evidence.

## 9. Hard Links vs Symbolic (Soft) Links

| Aspect | Hard Link | Symbolic (Soft) Link |
|---|---|---|
| What it points to | The same **inode** as the original file | The original file's **path** (a separate small file containing that path) |
| Survives original deletion? | Yes — data persists as long as any hard link exists (inode's link count > 0) | No — becomes a "broken link" pointing to nothing |
| Can span filesystems/partitions? | No — must be on the same filesystem | Yes |
| Can link to a directory? | No (with rare exceptions) | Yes |
| Appears as | Indistinguishable from the original file | A distinct file type (`l`), shown with an arrow in `ls -l` |

```bash
ln original.txt hardlink.txt         # create a hard link
ln -s original.txt symlink.txt        # create a symbolic link

ls -li original.txt hardlink.txt      # same inode number
```

**Why deleting a file doesn't always free its space:** `rm` only removes a directory entry (the name-to-inode mapping) and decrements the inode's **link count** — the actual data blocks are only freed once the link count reaches zero AND no process still has the file open. This is exactly why a large deleted log file can still occupy disk space if a running process still holds it open (`lsof | grep deleted` reveals these).

## 10. Interview Questions

1. What does "everything is a file" mean in the context of Linux? → **Hardware devices, processes, sockets, and IPC channels are all accessible via the same file-based interface, not just documents**
2. What information does an inode store, and what does it NOT store? → **Stores metadata (permissions, owner, size, timestamps, data block pointers); does NOT store the filename**
3. What's the fundamental difference between a hard link and a symbolic link? → **A hard link points to the same inode as the original (same data); a symbolic link points to the original's path (a separate reference)**
4. Why can't hard links span across different filesystems/partitions but symlinks can? → **Inode numbers are only unique within a single filesystem; symlinks just store a path string, which works across filesystems**
5. What are the three main file timestamps, and which one is most associated with "timestomping" in forensics? → **mtime, ctime, atime — all three (and crtime where present) can be manipulated by attackers to obscure activity timelines**
6. Why might disk space not be freed immediately after deleting a large file? → **If a running process still has the file open, the underlying data blocks aren't released until the link count reaches zero and no process holds it open**

## 11. Key Points

- Linux has **7 file types**: regular, directory, symlink, character device, block device, socket, named pipe (FIFO).
- An **inode** stores all file metadata (permissions, owner, size, timestamps, data pointers) — **except the filename**, which lives in the containing directory.
- **Hard links** share the same inode (same data); **symlinks** are separate files pointing to a path.
- **mtime/ctime/atime** are critical for forensic timeline reconstruction — and a target for anti-forensic "timestomping."
- Deleting a file just removes a directory entry and decrements the inode's link count — data isn't freed until the count hits zero and no process has it open.

---
*Related: [[Linux_File_System_Hierarchy]], [[File_Permissions]], [[Disk_and_Storage_Management]]*
