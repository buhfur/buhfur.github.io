---
title: Sed
parent: Utils
grand_parent: Linux
nav_order: 200
layout: default
---

# sed Command Guide

## Overview
`sed` (Stream Editor) is a powerful Unix utility for parsing and transforming text using simple commands and regular expressions. It is commonly used for tasks such as search-and-replace, line deletion, and text substitution in scripts or pipelines.

---

## Basic Syntax
```bash
sed [options] 'command' file
```

Common options:
| Option | Description |
|---------|-------------|
| `-n` | Suppress automatic printing of pattern space |
| `-e` | Add script to execute |
| `-i` | Edit file in place |
| `-r` | Use extended regular expressions |
| `-f` | Read sed commands from a file |

---

## Basic Examples

### Print Specific Lines
```bash
sed -n '5p' file.txt        # Print line 5
sed -n '1,3p' file.txt      # Print lines 1 to 3
sed -n '/pattern/p' file.txt  # Print lines matching pattern
```

### Replace Text
```bash
sed 's/foo/bar/' file.txt             # Replace first occurrence of 'foo' with 'bar'
sed 's/foo/bar/g' file.txt            # Replace all occurrences on each line
sed -i 's/foo/bar/g' file.txt         # Replace in file directly
sed 's/[0-9]//g' file.txt             # Remove all digits
```

### Delete Lines
```bash
sed '1d' file.txt             # Delete first line
sed '1,5d' file.txt           # Delete lines 1 through 5
sed '/pattern/d' file.txt     # Delete lines matching pattern
```

### Insert / Append Text
```bash
sed '2i\Inserted before line 2' file.txt    # Insert before line 2
sed '3a\Appended after line 3' file.txt     # Append after line 3
```

### Modify Specific Lines
```bash
sed '5s/old/new/' file.txt      # Replace only on line 5
sed '/error/s/fail/pass/g' file.txt  # Replace text only in lines containing 'error'
```

---

## Using sed with Pipelines

### Replace Text from stdin
```bash
echo "hello world" | sed 's/world/sed/'
```

### Remove Blank Lines
```bash
cat file.txt | sed '/^$/d'
```

### Number All Lines
```bash
sed = file.txt | sed 'N;s/\n/\t/'
```

---

## Regular Expressions in sed
- `.` — Matches any character
- `^` — Start of line
- `$` — End of line
- `*` — Zero or more occurrences
- `\{n,m\}` — Repetition count
- `[]` — Character class
- `()` — Grouping (use `-r` or `-E` for extended)
- `|` — Alternation (OR) in extended regex

Example:
```bash
sed -r 's/(cat|dog)/animal/g' file.txt
```

---

## Advanced Examples

### Replace Only on Specific Line Range
```bash
sed '10,20s/error/fixed/g' log.txt
```

### Change Text Between Patterns
```bash
sed '/BEGIN/,/END/s/foo/bar/g' file.txt
```

### Print Lines Not Matching a Pattern
```bash
sed -n '/pattern/!p' file.txt
```

### Replace Using Variables in Shell
```bash
search="foo"
replace="bar"
sed -i "s/${search}/${replace}/g" file.txt
```

### Remove Comments and Blank Lines
```bash
sed '/^#/d;/^$/d' config.txt
```

### Replace Tabs with Spaces
```bash
sed 's/\t/    /g' file.txt
```

---

## Tips and Tricks

### 1. Backup Before Editing In-Place
```bash
sed -i.bak 's/foo/bar/g' file.txt
```

### 2. Chain Multiple Commands
```bash
sed -e 's/foo/bar/' -e '/baz/d' file.txt
```

### 3. Read from File with Commands
```bash
sed -f script.sed file.txt
```

### 4. Use & to Reuse the Matched Pattern
```bash
echo "error" | sed 's/.*/(&)/'     # Output: (error)
```

### 5. Debug Complex Expressions
```bash
sed -n 'l' file.txt
```

### 6. Replace with Newline Characters
```bash
sed 's/;/\n/g' file.txt
```

### 7. Swap Two Words
```bash
echo "foo bar" | sed 's/\(.*\) \(.*\)/\2 \1/'
```

### 8. Extract Text Between Delimiters
```bash
echo "<tag>value</tag>" | sed -n 's/.*<tag>\(.*\)<\/tag>.*/\1/p'
```

### 9. Replace nth Occurrence in Line
```bash
sed 's/foo/bar/2' file.txt   # Replace 2nd occurrence only
```

### 10. Work with Multiline Patterns (GNU sed only)
```bash
sed ':a;N;$!ba;s/old/new/g' file.txt
```

---

## Common Use Cases

| Task | Command |
|------|----------|
| Remove leading whitespace | `sed 's/^\s*//' file.txt` |
| Remove trailing whitespace | `sed 's/\s*$//' file.txt` |
| Double-space file | `sed G file.txt` |
| Print only unique lines | `sed '$!N; /\n.*$/!p; D' file.txt` |
| Convert lowercase to uppercase | `sed 's/[a-z]/\U&/g' file.txt` |
| Convert uppercase to lowercase | `sed 's/[A-Z]/\L&/g' file.txt` |

---

## References
- GNU sed manual: <https://www.gnu.org/software/sed/manual/sed.html>
- `man sed`
