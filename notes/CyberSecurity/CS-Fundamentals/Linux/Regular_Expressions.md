## 1. Introduction

**Regular expressions (regex)** are patterns used to match, search, and manipulate text — the engine behind `grep`, `sed`, `awk`, and countless security tools (log analysis, YARA rules, IDS signatures, input validation). Linux tools historically support two regex dialects, which is a common source of confusion.

## 2. BRE vs ERE — The Two POSIX Dialects

| Dialect | Full Name | Metacharacters Need Escaping? | Used By (default) |
|---|---|---|---|
| BRE | Basic Regular Expression | Yes — `(`, `)`, `{`, `}`, `+`, `?`, `\|` need a backslash to have special meaning | `grep`, `sed` (without flags) |
| ERE | Extended Regular Expression | No — these characters are special by default | `grep -E` (or `egrep`), `awk` |

```bash
grep 'a\{2,3\}' file.txt      # BRE: literal braces need escaping
grep -E 'a{2,3}' file.txt      # ERE: no escaping needed
```

Most modern usage defaults to `grep -E` or tools that use ERE-like syntax (including PCRE in many contexts) to avoid this escaping headache entirely.

## 3. Basic Metacharacters

| Metacharacter | Meaning |
|---|---|
| `.` | Any single character |
| `*` | Zero or more of the preceding character/group |
| `+` | One or more (ERE) |
| `?` | Zero or one (ERE) |
| `^` | Start of line/string |
| `$` | End of line/string |
| `[...]` | Character class — any one character in the set |
| `[^...]` | Negated character class — any one character NOT in the set |
| `\|` | Alternation (OR) (ERE) |
| `(...)` | Grouping (ERE) |
| `{n,m}` | Between n and m repetitions |
| `\` | Escape a metacharacter to match it literally |

## 4. Character Classes

| Pattern | Matches |
|---|---|
| `[abc]` | a, b, or c |
| `[a-z]` | Any lowercase letter |
| `[A-Z]` | Any uppercase letter |
| `[0-9]` | Any digit |
| `[a-zA-Z0-9]` | Any alphanumeric character |
| `[^0-9]` | Any character that is NOT a digit |

**POSIX character classes (portable, locale-aware):**

| Class | Equivalent |
|---|---|
| `[:digit:]` | `[0-9]` |
| `[:alpha:]` | `[a-zA-Z]` |
| `[:alnum:]` | `[a-zA-Z0-9]` |
| `[:space:]` | Whitespace characters |
| `[:upper:]` | Uppercase letters |
| `[:lower:]` | Lowercase letters |

```bash
grep '[[:digit:]]\{3\}' file.txt      # must double-bracket: outer [] for the class, inner [::] for the named set
```

## 5. Anchors and Boundaries

```bash
grep '^root' /etc/passwd            # lines STARTING with "root"
grep 'bash$' /etc/passwd              # lines ENDING with "bash"
grep '^$' file.txt                      # match completely empty lines
grep -w 'cat' file.txt                    # word boundary — matches "cat" but not "concatenate"
```

## 6. Quantifiers

| Pattern | Meaning |
|---|---|
| `a*` | Zero or more "a" |
| `a+` | One or more "a" (ERE) |
| `a?` | Zero or one "a" (ERE) |
| `a{3}` | Exactly 3 "a" |
| `a{2,5}` | Between 2 and 5 "a" |
| `a{2,}` | 2 or more "a" |

## 7. PCRE (Perl-Compatible Regular Expressions)

A more powerful, widely-adopted regex dialect (used by `grep -P`, most programming languages, and many security tools like Snort/Suricata signatures) adding features BRE/ERE lack:

| Feature | Syntax | Meaning |
|---|---|---|
| Digit shorthand | `\d` | Equivalent to `[0-9]` |
| Word character | `\w` | Equivalent to `[a-zA-Z0-9_]` |
| Whitespace | `\s` | Any whitespace character |
| Non-digit/word/space | `\D`, `\W`, `\S` | Negated versions |
| Lazy quantifier | `*?`, `+?` | Non-greedy matching (shortest possible match) |
| Lookahead | `(?=...)`, `(?!...)` | Assert what follows without consuming it |
| Lookbehind | `(?<=...)`, `(?<!...)` | Assert what precedes without consuming it |

```bash
grep -P '\d{3}-\d{4}' file.txt        # PCRE digit shorthand
```

## 8. Practical Security/Log Analysis Patterns

```bash
# Match an IPv4 address (simplified, not fully strict)
grep -E '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' log.txt

# Match an email address
grep -Eo '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}' file.txt

# Match failed SSH login attempts
grep -E 'Failed password for .* from [0-9.]+' /var/log/auth.log

# Match lines with a suspicious base64-looking blob (potential obfuscated payload)
grep -E '[A-Za-z0-9+/]{40,}={0,2}' suspicious.log

# Match a MAC address
grep -E '([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}' file.txt
```

## 9. Regex in sed and awk

```bash
sed -E 's/[0-9]+/NUMBER/g' file.txt         # -E enables ERE in sed, matching grep -E behavior
awk '/^[0-9]+$/ {print}' file.txt              # awk natively uses ERE-like syntax
awk '$0 ~ /error/ {print}' file.txt              # explicit match operator ~
```

## 10. Common Regex Pitfalls

| Pitfall | Explanation |
|---|---|
| Forgetting `-E`/`-P` | Using ERE/PCRE syntax with plain `grep` silently fails to match as expected |
| Greedy matching surprises | `.*` matches as much as possible by default, which can overshoot across multiple expected boundaries |
| Not anchoring | Forgetting `^`/`$` can cause unintended partial matches anywhere in the line |
| Special characters in literal searches | Searching for a literal `.` or `*` without escaping it matches far more than intended |

## 11. Interview Questions

1. What's the key difference between BRE and ERE? → **In BRE, metacharacters like `+`, `?`, `(`, `)` need a backslash to be special; in ERE they don't**
2. What does `grep -E` do differently from plain `grep`? → **Enables Extended Regular Expression syntax, avoiding the need to escape metacharacters like `+`, `?`, `|`**
3. What does `\d` match in PCRE, and does plain POSIX grep support it? → **Any digit (equivalent to `[0-9]`); plain POSIX grep does NOT support it — requires `grep -P`**
4. What's the difference between `^` and `$` as regex anchors? → **`^` anchors to the start of a line/string; `$` anchors to the end**
5. Why would you use `-w` with grep when searching for a short word like "cat"? → **To match only the whole word "cat," not as a substring within longer words like "concatenate"**
6. What regex would you use to find IPv4-address-like patterns in a log file? → **`grep -E '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'`**

## 12. Key Points

- Linux tools use two POSIX dialects: **BRE** (default grep/sed, requires escaping) and **ERE** (`grep -E`, awk, no escaping needed).
- **PCRE** (`grep -P`) adds powerful shorthand (`\d`, `\w`, `\s`) and lookaheads/lookbehinds, widely used beyond just grep (programming languages, IDS signatures).
- Core building blocks: **anchors** (`^`/`$`), **character classes** (`[...]`), **quantifiers** (`*`, `+`, `{n,m}`), and **grouping/alternation** (`(...)`/`|`).
- Regex is the engine behind `grep`, `sed`, `awk`, and is essential for log analysis, forensic pattern matching, and building detection rules.

---
*Related: [[Text_Processing_Commands]], [[Log_Management]], [[Shell_Scripting]]*
