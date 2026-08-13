## 1. Introduction

Publicly posted files — PDFs, Office documents, images — routinely carry hidden **metadata** that the visible content itself never shows: author names, software versions, internal file paths, and sometimes GPS coordinates. Extracting it is a fully passive technique (you're only reading data already published) that frequently reveals more than the document's actual content.

## 2. What Metadata Commonly Reveals

| Metadata Field | Typical Value / Risk |
|---|---|
| Author / Creator | Real employee names, sometimes usernames matching internal login conventions |
| Software and version | Revealing which OS/Office version created the file (e.g., "Microsoft Office 16.0 on Windows 10") — useful for OS/software fingerprinting |
| File path | Local save paths sometimes reveal internal folder structure, usernames, or server share names (e.g., `C:\Users\jsmith\Documents\...`) |
| Last modified by | A second employee name, if the file was edited by someone other than the original author |
| GPS coordinates (images) | EXIF data on photos can reveal the exact physical location a photo was taken, relevant for physical security assessments |
| Printer/device identifiers | Some formats embed the specific device used to generate the file |

## 3. Extraction Tools

| Tool | Use Case |
|---|---|
| ExifTool | The standard command-line tool for reading metadata across nearly every file format |
| FOCA | Purpose-built for bulk document metadata harvesting and analysis, historically popular for this exact recon use case |
| Metagoofil | Automates finding and downloading public documents from a domain, then extracts metadata from all of them in one pass |

```
exiftool document.pdf
```

## 4. Practical Workflow

```
1. Use Google Dorking (filetype:pdf, filetype:docx, etc.) to find publicly posted target documents
2. Bulk-download the discovered files (Metagoofil automates steps 1-2 together)
3. Run ExifTool/FOCA across the set
4. Aggregate: usernames found → feed into Email_Harvesting naming convention guesses
             software/OS versions found → feed into later vulnerability analysis
             file paths found → reveal internal naming/share conventions
```

## 5. Interview Questions

1. Why is metadata extraction considered a passive recon technique? → **It only reads data embedded in files that are already publicly posted — nothing is sent to or requested from the target's live infrastructure**
2. Give two examples of information metadata can leak beyond a document's visible content. → **Any two of: author/creator name, software/OS version used to create it, local file path revealing internal folder structure, GPS coordinates on images, last-modified-by username**
3. What does Metagoofil automate that ExifTool alone does not? → **Metagoofil automates finding and downloading public documents from a target domain first, then extracts metadata from the whole batch — ExifTool only extracts metadata from files you already have**
4. How might a file path found in metadata (e.g., `C:\Users\jsmith\...`) be useful later in the engagement? → **It can reveal a real username/naming convention, useful for guessing valid login usernames, and confirms internal folder/share naming patterns**

## 6. Key Points

- Publicly posted documents routinely carry **hidden metadata** — authors, software versions, file paths, GPS data — beyond their visible content.
- **ExifTool** is the standard extraction tool; **FOCA** and **Metagoofil** specialize in bulk document harvesting and analysis specifically for recon.
- Discovered usernames and file paths often feed directly into [[Email_Harvesting]]'s naming-convention guessing and later credential-related testing.
- A purely passive technique — entirely reading data already published, no interaction with target infrastructure.

---
*Related: [[Google_Dorking]], [[Email_Harvesting]], [[OSINT_Framework_and_Tools]], [[Passive_vs_Active_Recon]]*
