---

layout: default
title: "Markdown Syntax Guide"

---

# Markdown Syntax Guide

Markdown is a lightweight markup language for creating formatted text using a plain-text editor.

---

## Headings
Use `#` for headings. Add more `#` for smaller headings.

```markdown
# H1
## H2
### H3
#### H4
##### H5
###### H6
```

### Output:
# H1
## H2
### H3
#### H4
##### H5
###### H6

---

## Emphasis
- **Bold:** Use `**text**` or `__text__`.
- *Italic:* Use `*text*` or `_text_`.
- ~~Strikethrough:~~ Use `~~text~~`.

```markdown
**Bold**
*Italic*
~~Strikethrough~~
```

### Output:
**Bold**  
*Italic*  
~~Strikethrough~~

---

## Lists
### Unordered List
Use `-`, `*`, or `+`.

```markdown
- Item 1
- Item 2
  - Subitem 2.1
  - Subitem 2.2
```

### Ordered List
Use numbers followed by a period.

```markdown
1. First item
2. Second item
   1. Subitem 2.1
   2. Subitem 2.2
```

---

## Links
Create hyperlinks using `[link text](URL)`.

```markdown
[OpenAI](https://www.openai.com)
```

### Output:
[OpenAI](https://www.openai.com)

---

## Images
Embed images using `![alt text](URL)`.

```markdown
![OpenAI Logo](https://via.placeholder.com/150)
```

---

## Code
### Inline Code
Wrap code with backticks: `` `code` ``.

### Block Code
Use triple backticks for code blocks:

```markdown
\`\`\`language
Code block
\`\`\`
```

Example:
```python
def hello_world():
    print("Hello, world!")
```

---

## Blockquotes
Use `>` to create blockquotes.

```markdown
> This is a blockquote.
>> Nested blockquote.
```

### Output:
> This is a blockquote.  
>> Nested blockquote.

---

## Horizontal Rules
Use `---`, `***`, or `___`.

```markdown
---
```

---

## Tables
Create tables with `|` and `-`.

```markdown
| Header 1 | Header 2 |
|----------|----------|
| Row 1    | Data     |
| Row 2    | More Data|
```

### Output:
| Header 1 | Header 2 |
|----------|----------|
| Row 1    | Data     |
| Row 2    | More Data|

---

## Task Lists
Use `- [ ]` for unchecked and `- [x]` for checked.

```markdown
- [ ] Task 1
- [x] Task 2
```

### Output:
- [ ] Task 1  
- [x] Task 2

---

## Escaping Characters
Use a backslash `\` to escape Markdown syntax.

```markdown
\*This text is not italicized.*
```

---

## Inline HTML
You can include raw HTML for advanced formatting.

```markdown
<p>This is a paragraph in HTML.</p>
```

---

## Footnotes
Use `[^1]` for footnotes.

```markdown
This is a sentence with a footnote.[^1]

[^1]: This is the footnote content.
```

---

## Definition Lists
```markdown
Term
: Definition
```

---

## Additional Notes
Markdown files typically have the `.md` or `.markdown` extension.
