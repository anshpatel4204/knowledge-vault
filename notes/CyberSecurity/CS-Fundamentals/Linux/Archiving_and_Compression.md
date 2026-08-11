## 1. Introduction

**Archiving** bundles multiple files/directories into a single file (preserving structure and metadata), while **compression** reduces file size by encoding redundancy more efficiently — two related but distinct operations that Linux tools often combine in one command. This is everyday-essential for backups, software distribution, log archival, and transferring evidence in forensic work.

## 2. Archiving vs Compression — The Distinction

| Operation | Purpose | Example Tool |
|---|---|---|
| Archiving | Combine multiple files into one, preserving structure/permissions/metadata | `tar` |
| Compression | Reduce file size | `gzip`, `bzip2`, `xz`, `zip` |

`tar` itself only archives — it doesn't compress by default, but is almost always used together with a compression tool in one pipeline/flag combination (hence ".tar.gz" being extremely common).

## 3. tar — The Standard Archiving Tool

```bash
tar -cvf archive.tar file1 file2 dir/       # Create a tar archive
tar -xvf archive.tar                          # eXtract an archive
tar -tvf archive.tar                            # List (Table of) contents without extracting
tar -rvf archive.tar newfile.txt                  # Append a file to an existing archive
```

| Flag | Meaning |
|---|---|
| `-c` | Create |
| `-x` | Extract |
| `-t` | List/table of contents |
| `-v` | Verbose (show files as processed) |
| `-f` | Specify the archive filename (almost always required, must come last among combined flags) |
| `-r` | Append to an existing archive |
| `-C` | Change to a directory before extracting (useful for controlling extraction location) |

## 4. tar with Compression

```bash
tar -czvf archive.tar.gz dir/       # Create + gzip compress
tar -xzvf archive.tar.gz               # Extract a gzip-compressed archive
tar -cjvf archive.tar.bz2 dir/            # Create + bzip2 compress
tar -xjvf archive.tar.bz2                    # Extract a bzip2 archive
tar -cJvf archive.tar.xz dir/                   # Create + xz compress
tar -xJvf archive.tar.xz                           # Extract an xz archive
```

| Flag | Compression |
|---|---|
| `-z` | gzip (.tar.gz / .tgz) |
| `-j` | bzip2 (.tar.bz2) |
| `-J` | xz (.tar.xz) |

## 5. Compression Algorithm Comparison

| Tool | Extension | Speed | Compression Ratio | Notes |
|---|---|---|---|---|
| gzip | `.gz` | Fast | Moderate | Most common, widely compatible, good default |
| bzip2 | `.bz2` | Slower | Better than gzip | Good for text-heavy data |
| xz | `.xz` | Slowest | Best | High compression ratio, common for software distribution |
| zip | `.zip` | Fast | Moderate | Cross-platform (Windows-native compatibility), archives AND compresses in one format (unlike tar) |

**General rule:** gzip for speed, xz for maximum compression ratio (at the cost of time/CPU), zip when cross-platform (especially Windows) compatibility matters.

## 6. gzip / gunzip

```bash
gzip file.txt              # compress (replaces file.txt with file.txt.gz)
gunzip file.txt.gz            # decompress (replaces file.txt.gz with file.txt)
gzip -k file.txt                # keep the original file after compressing
zcat file.txt.gz                  # view a gzipped file's contents without fully decompressing it
```

Note: `gzip` compresses a **single file** — this is exactly why it's almost always paired with `tar` first, to bundle multiple files/directories into one archive before compressing.

## 7. bzip2 / xz (Similar Single-File Pattern)

```bash
bzip2 file.txt
bunzip2 file.txt.bz2

xz file.txt
unxz file.txt.xz
```

## 8. zip / unzip

Unlike gzip/bzip2/xz, `zip` archives **and** compresses multiple files in one step, natively (no separate `tar` step needed) — and is broadly compatible with Windows.

```bash
zip archive.zip file1.txt file2.txt         # add specific files
zip -r archive.zip directory/                  # recursive, zip an entire directory
unzip archive.zip                                # extract
unzip -l archive.zip                               # list contents without extracting
unzip -o archive.zip                                 # overwrite existing files without prompting
```

## 9. Password-Protecting Archives

```bash
zip -e -r secure.zip directory/          # password-protect a zip archive (prompts for password)
7z a -pPASSWORD archive.7z directory/       # using 7-Zip (if installed), stronger encryption than zip's legacy scheme
```

**Security note:** classic zip encryption is cryptographically weak by modern standards (see [[Cryptographic_Attacks]] in the Cryptography folder) — for genuinely sensitive data, prefer GPG-encrypted archives or 7-Zip's AES-256 option instead.

```bash
tar -czvf - directory/ | gpg -c > archive.tar.gz.gpg      # archive, then encrypt with GPG
gpg -d archive.tar.gz.gpg | tar -xzvf -                      # decrypt and extract in one pipeline
```

## 10. Practical Examples

```bash
# Back up a directory with a timestamped filename
tar -czvf backup_$(date +%Y%m%d).tar.gz /home/ansh/documents

# Extract to a specific directory
tar -xzvf archive.tar.gz -C /opt/extracted/

# Archive only files modified in the last 7 days
find /var/log -mtime -7 -print0 | tar -czvf recent_logs.tar.gz --null -T -

# Check an archive's contents before extracting (important for untrusted archives)
tar -tzvf suspicious_archive.tar.gz
```

## 11. Security Considerations

| Concern | Description |
|---|---|
| Path traversal ("zip slip") | A maliciously crafted archive can contain entries with `../` in their paths, potentially overwriting files outside the intended extraction directory when extracted carelessly |
| Archive bombs | A tiny compressed file that expands to an enormous size when decompressed, used to exhaust disk space/resources (a form of DoS) |
| Always inspect before extracting | Use `tar -tvf` or `unzip -l` to preview an untrusted archive's contents/paths before actually extracting it |
| Weak zip encryption | Legacy zip password protection is crackable — don't rely on it for genuinely sensitive data |

## 12. Interview Questions

1. What's the difference between archiving and compression? → **Archiving combines multiple files into one (preserving structure); compression reduces file size — tar does the former, gzip/bzip2/xz do the latter**
2. Why is `tar` almost always paired with gzip/bzip2/xz? → **tar only bundles files, it doesn't compress on its own — compression tools operate on a single file, so files are archived into one first**
3. Which compression tool typically gives the best compression ratio, at the cost of speed? → **xz**
4. What's unique about `zip` compared to gzip/tar in terms of workflow? → **zip both archives AND compresses multiple files natively in one step, without needing a separate tar step**
5. What is an archive/zip bomb? → **A small compressed file engineered to expand to an enormous size on decompression, exhausting disk/resources**
6. Why should you preview an untrusted archive's contents before extracting it? → **To check for path traversal ("zip slip") entries that could overwrite files outside the intended extraction directory**

## 13. Key Points

- **tar** archives (bundles) files; **gzip/bzip2/xz** compress — commonly combined (`tar -czvf`, `-cjvf`, `-cJvf`).
- Compression trade-off: **gzip** (fast, moderate), **xz** (slow, best ratio), **zip** (cross-platform, archives+compresses natively).
- Always preview (`-t`/`-l`) untrusted archives before extraction to guard against path traversal ("zip slip") and archive bombs.
- Legacy **zip encryption is weak** — use GPG or 7-Zip AES-256 for genuinely sensitive archived data.

---
*Related: [[Essential_Commands]], [[Disk_and_Storage_Management]], [[Linux_Security_Hardening]]*
