---

layout: default
title: "Regex Cheatsheet"

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

*This cheatsheet is a quick reference for common regular expression patterns.*

