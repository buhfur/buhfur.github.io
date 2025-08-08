---

layout: default
title: "Python"

---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

# Helpful Python Tools & Snippets

This page contains a collection of useful Python tools, libraries, and code snippets for quick reference.

---

## 1. Fix Indentation Errors (e.g., TabError)

### Using `autopep8`
`autopep8` can automatically fix indentation issues, including the dreaded:
```
TabError: inconsistent use of tabs and spaces in indentation
```
**Install and run:**
```bash
pip install autopep8
autopep8 --in-place --aggressive --aggressive your_script.py
```
- `--in-place` edits the file directly.
- `--aggressive` runs multiple formatting passes for better cleanup.

### Using `black` (opinionated formatter)
```bash
pip install black
black your_script.py
```
Black will enforce consistent 4-space indentation and clean up formatting.

### Quick Python script to replace tabs with spaces
```python
from pathlib import Path

def fix_indentation(file_path, spaces_per_tab=4):
    content = Path(file_path).read_text()
    fixed = content.replace("\t", " " * spaces_per_tab)
    Path(file_path).write_text(fixed)
    print(f"Indentation fixed for {file_path}")

# Example usage
fix_indentation("your_script.py")
```

---

## 2. Simple HTTP Server

Serve files from the current directory:
```bash
python3 -m http.server 8080
```

---

## 3. Virtual Environment Setup

```bash
python3 -m venv .venv
source .venv/bin/activate  # Linux/macOS
.venv\Scripts\activate   # Windows
```

---

## 4. JSON Pretty Print

From the command line:
```bash
python3 -m json.tool input.json
```

From Python code:
```python
import json

data = {"name": "Alice", "age": 30}
print(json.dumps(data, indent=4))
```

---

## 5. One-liner Web Scraping with Requests & BeautifulSoup

```bash
pip install requests beautifulsoup4
```
```python
import requests
from bs4 import BeautifulSoup

url = "https://example.com"
soup = BeautifulSoup(requests.get(url).text, "html.parser")
print(soup.title.string)
```

---

## 6. Simple File Search (like grep in Python)

```python
from pathlib import Path

def search_in_files(directory, text):
    for path in Path(directory).rglob("*.*"):
        try:
            if text in path.read_text(errors="ignore"):
                print(path)
        except:
            pass

search_in_files(".", "search_term")
```

---

## 7. Quick One-liner to Check for Port Availability

```python
import socket

def check_port(host, port):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        return s.connect_ex((host, port)) == 0

print(check_port("127.0.0.1", 22))
```

---

## 8. Python REPL with Tab Completion

```bash
python3 -m rlcompleter
```

Or add to `~/.pythonrc`:
```python
import rlcompleter, readline
readline.parse_and_bind("tab: complete")
```

---

## 9. Running Python from Clipboard

```bash
xclip -o -selection clipboard | python3
```

On Windows:
```powershell
Get-Clipboard | python
```

---

**Tip:** Keep this page updated as you find more useful tools and tricks.
