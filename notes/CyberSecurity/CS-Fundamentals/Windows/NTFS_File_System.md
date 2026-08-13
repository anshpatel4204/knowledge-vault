## 1. Introduction

**NTFS (New Technology File System)** has been Windows' primary filesystem since Windows NT 3.1 (1993), replacing the older FAT filesystem for anything beyond removable media. It adds journaling, per-object security (ACLs), large volume/file support, and metadata-rich forensic artifacts that make it central to both system administration and digital forensics/incident response.

## 2. NTFS vs FAT32 vs exFAT

| Feature | FAT32 | exFAT | NTFS |
|---|---|---|---|
| Max file size | 4 GB | ~16 EB | ~16 TB (practical) |
| Max volume size | ~2 TB | Very large | ~256 TB |
| Journaling | No | No | **Yes** |
| Permissions (ACLs) | No | No | **Yes** |
| Encryption (EFS) | No | No | **Yes** |
| Compression | No | No | **Yes** |
| Typical use today | USB drives (legacy) | USB/SD (modern, cross-OS) | Windows system/data volumes |

## 3. The Master File Table (MFT)

Every file and folder on an NTFS volume — including NTFS's own metadata files — has an entry in the **Master File Table (MFT)**, essentially a database describing the entire volume. Each MFT entry is 1KB and stores attributes (timestamps, permissions, and for small files, the file's actual data) directly.

| Concept | Description |
|---|---|
| MFT entry | A 1KB record describing one file/folder's metadata (and small file contents, if it fits — "resident" data) |
| Resident file | Data small enough to fit inside the MFT entry itself |
| Non-resident file | Data stored in separate clusters, referenced by pointers in the MFT entry |
| `$MFT` | The MFT's own entry describing itself — file 0 on every NTFS volume |

This design is why NTFS forensic tools (e.g., in [[Digital Forensics]]) can recover rich metadata even for deleted files — MFT entries often persist after deletion until overwritten.

## 4. NTFS Metadata Files

NTFS reserves the first several MFT entries for system metadata files, all prefixed with `$`:

| File | Purpose |
|---|---|
| `$MFT` | The Master File Table itself |
| `$MFTMirr` | Backup copy of the first few MFT entries |
| `$LogFile` | Transaction log — the basis of NTFS journaling |
| `$Bitmap` | Tracks which clusters on the volume are used/free |
| `$Boot` | Boot sector data for the volume |
| `$Secure` | Stores security descriptors (ACLs) shared across files |

## 5. Journaling — Why NTFS Survives Crashes

NTFS is a **journaling filesystem**: before making a metadata change (creating/deleting/moving a file), it first writes the intended change to `$LogFile`. If the system crashes mid-operation, NTFS replays or rolls back the journal on next mount, keeping the filesystem's metadata structurally consistent (though it doesn't guarantee individual file *data* survives, only metadata integrity) — a major reliability advantage over the older FAT filesystem.

## 6. Alternate Data Streams (ADS)

NTFS allows a single file to have multiple **data streams** — the "main" content plus any number of hidden, named additional streams, addressed as `filename:streamname`.

```cmd
:: create a hidden alternate data stream
echo secret data > normalfile.txt:hidden.txt

:: view it
more < normalfile.txt:hidden.txt

:: list ADS on a file (PowerShell)
Get-Item normalfile.txt -Stream *
```

**Security relevance:** ADS is a well-known technique for hiding malicious payloads — the hidden stream doesn't show up in normal directory listings or affect the visible file's size, making it a classic (if now well-monitored) evasion and persistence trick.

## 7. NTFS Permissions and Attributes

NTFS stores per-object **Access Control Lists (ACLs)** as part of file/folder security descriptors — covered in depth in [[File_and_Folder_Permissions]]. It also supports standard file attributes:

```cmd
attrib file.txt              :: show attributes
attrib +h +s file.txt          :: set Hidden and System attributes
attrib -h -s file.txt           :: clear them
```

| Attribute | Meaning |
|---|---|
| R | Read-only |
| H | Hidden |
| S | System |
| A | Archive (needs backup) |
| I | Not indexed for search |

## 8. NTFS Timestamps (MACB)

Every file has four timestamps, frequently referenced in forensic timeline analysis (often abbreviated **MACB**):

| Timestamp | Meaning |
|---|---|
| **M**odified | Last time file content changed |
| **A**ccessed | Last time file was read (often disabled by default on modern Windows for performance) |
| **C**hanged/Created | Entry creation, or in `$MFT` context, last metadata change |
| **B**irth | Original creation time |

**Timestomping** — deliberately altering these timestamps (e.g., via `SetFile Time` APIs) — is a common anti-forensic technique attackers use to make malicious files blend in with legitimate system files.

## 9. Useful Commands

```cmd
fsutil fsinfo volumeinfo C:              :: volume info including filesystem type
fsutil fsinfo ntfsinfo C:                  :: detailed NTFS metadata (cluster size, MFT location)
chkdsk C: /f                                 :: check and fix filesystem errors
compact /c file.txt                            :: enable NTFS compression on a file
cipher /e file.txt                               :: encrypt a file using EFS
```

## 10. Interview Questions

1. What is the MFT, and why does it matter forensically? → **The Master File Table — a database of every file/folder's metadata on the volume; MFT entries frequently persist after "deletion," enabling file recovery/forensic analysis**
2. What makes NTFS a journaling filesystem? → **It logs intended metadata changes to `$LogFile` before committing them, allowing consistent recovery after a crash**
3. What is an Alternate Data Stream, and why is it a security concern? → **A hidden secondary data stream attached to a file (`file:stream`); attackers use it to conceal payloads since it's invisible in normal directory listings and doesn't change the visible file size**
4. What do the MACB timestamps represent, and what is "timestomping"? → **Modified/Accessed/Changed/Birth — the four NTFS timestamps; timestomping is deliberately falsifying them to evade forensic timeline analysis**
5. What is the difference between a resident and non-resident file in NTFS? → **Resident data is small enough to be stored directly inside the MFT entry; non-resident data lives in separate clusters referenced by the MFT entry**

## 11. Key Points

- NTFS has been the default Windows filesystem since NT 3.1, adding **journaling, ACLs, encryption (EFS), and large-volume support** over FAT.
- The **MFT** is the heart of NTFS — a metadata record for every object on the volume, central to Windows forensics.
- **Alternate Data Streams** and **timestomping** are two NTFS-specific techniques attackers use to hide data and evade detection — both are worth knowing from a blue-team perspective too.
- NTFS permissions (ACLs) are covered in depth in [[File_and_Folder_Permissions]]; the registry (Windows' other core structure) is covered in [[Windows_Registry]].

---
*Related: [[Windows_Basics]], [[File_and_Folder_Permissions]], [[Windows_Registry]], [[Windows_Privilege_Escalation]]*
