
# Regular Expressions (Regex) Cheatsheet

## Character Classes
| Pattern | Description |
|---------|-------------|
| `.`     | Any character except newline |
| `\d`    | Digit (0-9) |
| `\D`    | Not a digit |
| `\w`    | Word character (alphanumeric + `_`) |
| `\W`    | Not a word character |
| `\s`    | Whitespace character |
| `\S`    | Not a whitespace character |

## Anchors
| Pattern | Description |
|---------|-------------|
| `^`     | Start of string |
| `$`     | End of string |
| ``    | Word boundary |
| `\B`    | Not a word boundary |

## Quantifiers
| Pattern   | Description |
|-----------|-------------|
| `*`       | 0 or more times |
| `+`       | 1 or more times |
| `?`       | 0 or 1 time |
| `{n}`     | Exactly n times |
| `{n,}`    | n or more times |
| `{n,m}`   | Between n and m times |

## Groups and Ranges
| Pattern        | Description |
|----------------|-------------|
| `(abc)`        | Capture group |
| `(?:abc)`      | Non-capturing group |
| `[abc]`        | a, b, or c |
| `[^abc]`       | Not a, b, or c |
| `[a-z]`        | Lowercase a-z |
| `[A-Z]`        | Uppercase A-Z |
| `[0-9]`        | Digits 0-9 |

## Escaping Special Characters
| Pattern | Description |
|---------|-------------|
| `\`    | Escape character |
| `\.`    | Literal dot |
| `\*`   | Literal asterisk |

## Lookahead and Lookbehind (Advanced)
| Pattern        | Description |
|----------------|-------------|
| `(?=abc)`      | Positive lookahead |
| `(?!abc)`      | Negative lookahead |
| `(?<=abc)`     | Positive lookbehind |
| `(?<!abc)`     | Negative lookbehind |

## Substitution Syntax
| Pattern    | Description |
|------------|-------------|
| `$1`, `$2` | Refer to captured groups |

## Common Examples
| Pattern       | Description |
|---------------|-------------|
| `\d{3}-\d{2}-\d{4}` | US Social Security number |
| `[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}` | Email address |
| `https?://[^\s]+` | URL |

