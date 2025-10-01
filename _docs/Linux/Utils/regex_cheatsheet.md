---
title: Regex Cheatsheet & Useful Snippets
parent: Utils
grand_parent: Linux
nav_order: 140
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Regex Cheatsheet

Regular expressions (regex) are patterns used to match character combinations in strings.

## Basic Syntax

| Pattern   | Description                               |
|:----------|:------------------------------------------|
| `.`       | Any character except newline              |
| `^`       | Start of string                          |
| `$`       | End of string                            |
| `*`       | 0 or more of preceding element           |
| `+`       | 1 or more of preceding element           |
| `?`       | 0 or 1 of preceding element              |
| `{n}`     | Exactly n of preceding element           |
| `{n,}`    | n or more of preceding element           |
| `{n,m}`   | Between n and m of preceding element     |
| `|`       | OR                                       |
| `()`      | Grouping                                 |
| `[]`      | Character class                          |
| `[^ ]`    | Negated character class                  |
| `\`       | Escape special character                 |

## Common Escaped Characters

| Pattern   | Description                               |
|:----------|:------------------------------------------|
| `\d`      | Digit (0-9)                              |
| `\D`      | Non-digit                                |
| `\w`      | Word character (alphanumeric & underscore)|
| `\W`      | Non-word character                       |
| `\s`      | Whitespace                               |
| `\S`      | Non-whitespace                           |
| `\b`      | Word boundary                            |
| `\B`      | Non-word boundary                        |
| `\\`      | Literal backslash                        |

## Character Classes

| Pattern        | Description                               |
|:---------------|:------------------------------------------|
| `[abc]`        | Match 'a', 'b', or 'c'                   |
| `[a-z]`        | Match lowercase letters                  |
| `[A-Z]`        | Match uppercase letters                  |
| `[0-9]`        | Match digits                             |
| `[a-zA-Z0-9]`  | Match alphanumeric characters            |
| `[^abc]`       | Match any character except 'a','b','c'   |

## Assertions

| Pattern        | Description                               |
|:---------------|:------------------------------------------|
| `(?=...)`      | Positive lookahead                       |
| `(?!...)`      | Negative lookahead                       |
| `(?<=...)`     | Positive lookbehind                      |
| `(?<!...)`     | Negative lookbehind                      |

## Common Examples

| Pattern                                             | Description                               |
|:---------------------------------------------------|:------------------------------------------|
| `^hello`                                           | String starts with "hello"                |
| `world$`                                           | String ends with "world"                  |
| `\bword\b`                                         | Matches the whole word "word"             |
| `\d{3}-\d{2}-\d{4}`                                | Social Security Number pattern            |
| `^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$`| Email pattern                             |

---


# Advanced `sed` + Regex Snippets Cheatsheet

This cheatsheet covers advanced `sed` use-cases with powerful regular expressions for modifying files.

---

## Append a Link to the End of All Markdown Headers

Matches headers starting with either `#` or `##` and appends a `[🔗](#link)` at the end of each.

```bash
sed -E '/^#{1,2} /s/$/ [🔗](#link)/' file.md
```

---

## Replace "foo" with "bar" Only in Lines Starting with `##`

```bash
sed -E '/^## /s/foo/bar/g' file.md
```

---

## Insert a Line After Every Level-1 (`#`) Header

```bash
sed -E '/^# /a\
This is an inserted line.
' file.md
```

---

## Add a "Back to Top" Link After All `##` Headers

```bash
sed -E '/^## /a\
[Back to Top](#top)
' file.md
```

---

## Prepend a Tag to All Headers (Any Level)

```bash
sed -E '/^#+ /s/^/(TAG) /' file.md
```

---

## Match Headers with Optional Whitespace and Append a Link

Handles `# Header`, `## Header`, and even `#    Header`.

```bash
sed -E '/^#{1,2}[[:space:]]+/s/$/ [🔗](#link)/' file.md
```

---

## Remove Trailing Spaces from All Lines

```bash
sed -E 's/[[:space:]]+$//' file.md
```

---

## Insert "NEW HEADER" Before All `##` Headers

```bash
sed -E '/^## /i\
NEW HEADER
' file.md
```

---

##  Backup & Replace in One Go

Replaces `foo` with `bar` **in-place**, making a `.bak` backup.

```bash
sed -E -i.bak 's/foo/bar/g' file.md
```

---

##  Combine Multiple Modifications

Append a link to level-1 or level-2 headers, remove trailing spaces, and add a tag to each header.

```bash
sed -E '
  /^#{1,2} /s/$/ [🔗](#link)/
  s/[[:space:]]+$//
  /^#+ /s/^/(TAG) /
' file.md
```

---

## Key `sed` Concepts

- `-E`: Extended regex (no need to escape `+`, `?`, `{}`).
- `/pattern/`: Match lines containing `pattern`.
- `s/pattern/replacement/`: Substitute matched pattern.
- `a\`, `i\`: Append/Insert lines.
- `-i`: In-place editing.

---

*This cheatsheet is a quick reference for advanced `sed` with regex patterns.*



