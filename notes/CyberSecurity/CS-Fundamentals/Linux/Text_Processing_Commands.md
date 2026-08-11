## 1. Introduction

Linux's Unix philosophy (see [[Linux_Basics]]) treats **text streams as the universal interface** — logs, configuration files, command output, and network captures are all just text to be filtered, transformed, and analyzed. This note covers the core text-processing toolkit (`grep`, `sed`, `awk`, and friends) that powers everything from everyday admin tasks to log analysis and CTF/forensic work.

## 2. grep — Pattern Searching

Searches text for lines matching a pattern (literal string or regular expression — see [[Regular_Expressions]]).

| Flag | Effect |
|---|---|
| `-i` | Case-insensitive |
| `-v` | Invert match (show non-matching lines) |
| `-r` / `-R` | Recursive search through directories |
| `-n` | Show line numbers |
| `-c` | Count matching lines |
| `-l` | List only filenames with matches |
| `-E` | Extended regex (equivalent to `egrep`) |
| `-A n` / `-B n` / `-C n` | Show n lines After / Before / around (Context) each match |
| `-o` | Print only the matched portion, not the whole line |

```bash
grep "error" /var/log/syslog
grep -i "failed" auth.log
grep -rn "password" /etc/
grep -v "^#" config.conf              # exclude comment lines
grep -E "192\.168\.[0-9]+\.[0-9]+" log.txt
```

## 3. sed — Stream Editor

Performs find-and-replace and other line-based transformations on text, without opening an interactive editor.

```bash
sed 's/old/new/' file.txt              # replace first occurrence per line
sed 's/old/new/g' file.txt              # replace ALL occurrences per line (global)
sed -i 's/old/new/g' file.txt            # edit the file in-place
sed -n '5,10p' file.txt                   # print only lines 5-10
sed '/pattern/d' file.txt                  # delete lines matching pattern
sed 's/^/PREFIX: /' file.txt                # prepend text to every line
```

`sed -i` is extremely common (and extremely dangerous without `-i.bak` for a backup) when bulk-editing config files during system hardening.

## 4. awk — Pattern Scanning and Field Processing

A full text-processing language, especially powerful for working with **column/field-structured** data (like `/etc/passwd` or command output).

```bash
awk '{print $1}' file.txt                  # print the first field (whitespace-delimited by default)
awk -F: '{print $1}' /etc/passwd             # use ':' as the field separator
awk '{print $1, $3}' file.txt                 # print fields 1 and 3
awk '/error/ {print}' log.txt                  # print lines matching "error"
awk '{sum += $2} END {print sum}' data.txt       # sum a column
awk 'NR==5' file.txt                              # print only line 5 (NR = line/record number)
```

| Variable | Meaning |
|---|---|
| `$0` | The entire current line |
| `$1`, `$2`, ... | Individual fields |
| `NR` | Current line/record number |
| `NF` | Number of fields in the current line |

## 5. cut — Extract Columns

A simpler alternative to `awk` for straightforward column extraction.

```bash
cut -d: -f1 /etc/passwd          # -d sets delimiter, -f selects field(s)
cut -d, -f2,4 data.csv
cut -c1-5 file.txt                 # extract characters 1 through 5
```

## 6. sort and uniq

```bash
sort file.txt                       # alphabetical sort
sort -n file.txt                     # numeric sort
sort -r file.txt                      # reverse order
sort -k2 file.txt                      # sort by the 2nd field/column
sort -u file.txt                        # sort and remove duplicates

uniq file.txt                             # remove adjacent duplicate lines (input MUST be sorted first)
uniq -c file.txt                           # count occurrences of each line
sort file.txt | uniq -c | sort -rn          # classic "frequency count" combo
```

**Common combo:** counting the most frequent IPs in a log file:

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head
```

## 7. wc — Word/Line/Byte Count

```bash
wc -l file.txt      # count lines
wc -w file.txt       # count words
wc -c file.txt        # count bytes
wc -m file.txt         # count characters
```

## 8. tr — Translate/Delete Characters

```bash
echo "hello" | tr 'a-z' 'A-Z'          # uppercase conversion
tr -d '\r' < file.txt > cleaned.txt       # remove carriage returns (Windows line endings)
tr -s ' '                                   # squeeze repeated spaces into one
```

## 9. xargs — Building Commands from Input

Converts piped input into arguments for another command — bridges commands that don't natively accept piped input as arguments.

```bash
find . -name "*.tmp" | xargs rm             # delete all found files
cat urls.txt | xargs -I{} curl -O {}          # download each URL in a file
find . -name "*.log" | xargs grep "error"      # search across many found files
```

## 10. Combining Tools — Real-World Pipelines

```bash
# Top 10 IPs hitting a web server
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -10

# Find failed SSH login attempts and count by source IP
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn

# Extract all email addresses from a file
grep -Eo '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}' file.txt

# Search recursively for a hardcoded API key pattern
grep -rn "api_key" --include="*.py" /path/to/code/
```

## 11. Interview Questions

1. What's the difference between `grep` and `grep -v`? → **`-v` inverts the match, showing lines that do NOT match the pattern**
2. How would you replace all occurrences of "foo" with "bar" in a file, in-place? → **`sed -i 's/foo/bar/g' file.txt`**
3. How does `awk -F: '{print $1}' /etc/passwd` work? → **Sets `:` as the field separator, then prints the first field (username) of every line**
4. Why must input be sorted before piping into `uniq`? → **`uniq` only removes ADJACENT duplicate lines, so unsorted duplicates elsewhere in the file wouldn't be caught**
5. What does `xargs` do, and why is it needed with commands like `find`? → **It converts piped input into command-line arguments for another command, since not all commands read from stdin directly**
6. What's a real-world one-liner to find the top 10 most frequent IPs in a web server log? → **`awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -10`**

## 12. Key Points

- **grep** searches/filters text by pattern; **sed** performs stream-based find-and-replace; **awk** processes structured/columnar data.
- **cut**, **sort**, **uniq**, **wc**, **tr** are simpler, composable single-purpose tools — true to the Unix philosophy.
- The **`sort | uniq -c | sort -rn`** pipeline is a workhorse pattern for frequency analysis (log analysis, IP counting).
- **xargs** bridges piped output into commands that expect arguments, not stdin.
- These tools chain together via **pipes** (see [[Piping_Redirection_and_Operators]]) to build powerful one-liners for log analysis, forensics, and general administration.

---
*Related: [[Regular_Expressions]], [[Piping_Redirection_and_Operators]], [[Log_Management]], [[Shell_Scripting]]*
