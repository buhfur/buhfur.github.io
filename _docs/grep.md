---

layout: default
title: "Grep Snippets"

---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

### **Basic Search**
1. **Find a keyword in files:**
   ```bash
   grep "keyword" filename
   ```
2. **Case-insensitive search:**
   ```bash
   grep -i "keyword" filename
   ```

---

### **Recursive Search**
3. **Search recursively in all files:**
   ```bash
   grep -r "keyword" /path/to/directory
   ```
4. **Search recursively for a pattern while excluding specific directories:**
   ```bash
   grep -r --exclude-dir="dir_to_exclude" "keyword" /path/to/directory
   ```

---

### **Pattern Matching**
5. **Search using regular expressions:**
   ```bash
   grep -E "pattern1|pattern2" filename
   ```
6. **Match whole words only:**
   ```bash
   grep -w "word" filename
   ```

---

### **Filter Files**
7. **Find files containing a pattern:**
   ```bash
   grep -l "keyword" /path/to/directory/*
   ```
8. **Exclude files with certain extensions:**
   ```bash
   grep --exclude=*.log "keyword" /path/to/files/*
   ```

---

### **Output Control**
9. **Show line numbers with matches:**
   ```bash
   grep -n "keyword" filename
   ```
10. **Show only the matched part of the line:**
    ```bash
    grep -o "pattern" filename
    ```

---

### **Contextual Search**
11. **Show lines before and after matches:**
    ```bash
    grep -C 3 "keyword" filename  # 3 lines of context
    ```
12. **Show only lines before the match:**
    ```bash
    grep -B 3 "keyword" filename
    ```
13. **Show only lines after the match:**
    ```bash
    grep -A 3 "keyword" filename
    ```

---

### **Advanced Usage**
14. **Search for patterns in compressed files:**
    ```bash
    zgrep "keyword" file.gz
    ```
15. **Count occurrences of a pattern:**
    ```bash
    grep -c "keyword" filename
    ```
16. **Invert the match (find lines without the pattern):**
    ```bash
    grep -v "keyword" filename
    ```

---

### **Combining with Other Commands**
17. **Pipe output to grep:**
    ```bash
    ps aux | grep "process_name"
    ```
18. **Highlight matches in terminal output:**
    ```bash
    grep --color=always "keyword" filename
    ```

---

### **Debugging & Efficiency**
19. **Display file names along with matches in a directory:**
    ```bash
    grep -H "keyword" /path/to/directory/*
    ```
20. **Limit search depth in recursive searches:**
    ```bash
    grep -r --include=*.txt "keyword" --max-depth=2 /path/to/directory
    ```

---

These snippets cover a wide variety of use cases. Let me know if you'd like more examples for specific scenarios!

# Extended `grep` Snippets

---

### **File Type Filtering**
1. **Search only in specific file types:**
   ```bash
   grep --include=*.{c,h} "keyword" /path/to/directory/*
   ```
2. **Exclude specific file types:**
   ```bash
   grep --exclude=*.{log,tmp} "keyword" /path/to/directory/*
   ```

---

### **Binary Files**
3. **Ignore binary files in search:**
   ```bash
   grep --binary-files=without-match "keyword" /path/to/directory/*
   ```
4. **Search binary files:**
   ```bash
   grep --binary-files=text "keyword" /path/to/binary_file
   ```

---

### **Performance Optimizations**
5. **Limit output to first N matches:**
   ```bash
   grep -m N "keyword" filename
   ```
6. **Search large files without buffering:**
   ```bash
   grep --line-buffered "keyword" largefile
   ```

---

### **POSIX Character Classes**
7. **Search for digits:**
   ```bash
   grep "[[:digit:]]" filename
   ```
8. **Search for alphanumeric characters:**
   ```bash
   grep "[[:alnum:]]" filename
   ```

---

### **Custom Delimiters**
9. **Specify output delimiters:**
   ```bash
   grep -z "keyword" filename  # Null-separated output
   ```

---

### **Multiple Patterns**
10. **Search for multiple keywords (logical OR):**
    ```bash
    grep -e "keyword1" -e "keyword2" filename
    ```
11. **Search for multiple patterns from a file:**
    ```bash
    grep -f patterns.txt filename
    ```

---

### **Miscellaneous**
12. **Print file offsets of matches:**
    ```bash
    grep -b "keyword" filename
    ```
13. **Print the count of matches per file:**
    ```bash
    grep -c "keyword" filename
    ```
14. **Suppress error messages:**
    ```bash
    grep "keyword" filename 2>/dev/null
    ```

---

### **Multiline Matching**
15. **Search across multiple lines (using Perl-compatible grep):**
    ```bash
    grep -Pzo "pattern1.*?pattern2" filename
    ```

---

### **Using grep with xargs**
16. **Find files and search within them:**
    ```bash
    find /path/to/search -name "*.txt" | xargs grep "keyword"
    ```

---

### **Combining grep with sed and awk**
17. **Replace pattern in grep results:**
    ```bash
    grep "pattern" filename | sed 's/pattern/replacement/g'
    ```
18. **Count and format grep results:**
    ```bash
    grep "pattern" filename | awk '{count++} END {print "Total:", count}'
    ```

---

### **Environment Variables**
19. **Ignore case permanently by setting environment variable:**
    ```bash
    export GREP_OPTIONS='--ignore-case'
    ```

---

### **Highlight Matches in Less**
20. **Search while paging with highlights:**
    ```bash
    grep "keyword" filename | less -R
    ```

---


