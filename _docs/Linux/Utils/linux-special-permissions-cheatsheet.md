---
title: Linux Special File Permissions
parent: Utils
grand_parent: Linux
nav_order: 200
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---


# Linux Special Permissions – One Page Cheat Sheet

Linux supports **special permission bits** that extend standard read (`r`), write (`w`), and execute (`x`) permissions. These are frequently tested on exams and commonly used in real systems.

---

## Special Permission Bits

| Bit | Name | Numeric | Symbol | Applies To | Effect |
|----|-----|--------|-------|-----------|--------|
| SUID | Set User ID | `4` | `s` | Files | Runs program as file owner |
| SGID | Set Group ID | `2` | `s` | Files, Directories | Runs as group / forces group inheritance |
| Sticky | Sticky Bit | `1` | `t` | Directories | Only owner can delete files |

---

## Permission Positions

```
-rwsr-sr-t
 ||| ||| |||
 ||| ||| ||└─ others
 ||| ||└──── group
 ||└──────── user
```

---

## Common Examples

| Path | Permissions | Meaning |
|----|------------|--------|
| `/usr/bin/passwd` | `-rwsr-xr-x` | SUID root binary |
| `/tmp` | `drwxrwxrwt` | Sticky bit directory |
| `project/` | `drwxrwsr-x` | SGID group inheritance |

---

## Setting Permissions

### Symbolic Mode
```bash
chmod u+s file      # SUID
chmod g+s dir       # SGID
chmod +t dir        # Sticky
```

### Numeric Mode
```bash
chmod 4755 file     # SUID
chmod 2755 dir      # SGID
chmod 1777 /tmp     # Sticky
chmod 3775 shared/  # SGID + Sticky
```

---

## Numeric Breakdown

| Digit | Meaning |
|-----|--------|
| `4xxx` | SUID |
| `2xxx` | SGID |
| `1xxx` | Sticky |
| `7xxx` | All special bits |

---

## Uppercase vs Lowercase Permission Bits

| Symbol | Meaning |
|------|--------|
| `s` | Special bit + execute set |
| `S` | Special bit, **execute not set** |
| `t` | Sticky bit + execute |
| `T` | Sticky bit, **execute not set** |

Example:
```bash
-rwSr--r--  # SUID set, but file is not executable
```

---

## Verification Commands

```bash
ls -l
stat file
find / -perm /6000     # Find SUID and SGID files
find / -perm /1000     # Find sticky directories
```

---

## RHCSA Exam Reminders

- Sticky bit is almost always associated with `/tmp`
- SGID on directories enforces group collaboration
- SUID binaries should be rare and audited
- Know **numeric vs symbolic** permission setting cold

---

## References

- `man chmod`
- `man ls`
- `man stat`
