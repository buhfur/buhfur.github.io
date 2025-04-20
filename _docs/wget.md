---

layout: default
title: "Wget Snippets"
nav_order: 16

---
# Useful `wget` Snippets

The `wget` utility is a powerful tool for downloading files and interacting with web resources from the command line. Here are some practical snippets to make your workflow more efficient.

---

## 1. Download a Single File

```bash
wget https://example.com/file.zip
```

This downloads `file.zip` from the specified URL into the current directory.

---

## 2. Save File to a Specific Location

```bash
wget -P /path/to/directory https://example.com/file.zip
```

The `-P` option specifies the directory where the downloaded file will be saved.

---

## 3. Save with a Custom Filename

```bash
wget -O custom_name.zip https://example.com/file.zip
```

The `-O` option lets you define a custom name for the downloaded file.

---

## 4. Download Multiple Files

### From a List in a File

```bash
wget -i file_list.txt
```

The `-i` option specifies a file (`file_list.txt`) containing URLs, each on a new line.

### From Multiple URLs

```bash
wget https://example.com/file1.zip https://example.com/file2.zip
```

Simply list multiple URLs as arguments.

---

## 5. Resume a Download

```bash
wget -c https://example.com/largefile.zip
```

The `-c` option resumes a partially downloaded file. Useful for large downloads interrupted mid-way.

---

## 6. Download Entire Websites

```bash
wget --mirror --convert-links --adjust-extension --page-requisites --no-parent https://example.com/
```

This creates a local copy of a website with:
- `--mirror`: Enables mirroring mode.
- `--convert-links`: Converts links for local viewing.
- `--adjust-extension`: Adds the proper file extension.
- `--page-requisites`: Downloads all necessary files like CSS, JS, and images.
- `--no-parent`: Prevents traversing to parent directories.

---

## 7. Set a User-Agent String

```bash
wget --user-agent="Mozilla/5.0" https://example.com/file.zip
```

The `--user-agent` option sets the User-Agent header, useful for bypassing restrictions on default `wget` headers.

---

## 8. Limit Download Speed

```bash
wget --limit-rate=500k https://example.com/file.zip
```

The `--limit-rate` option limits download speed. For example, `500k` limits the download speed to 500 KB/s.

---

## 9. Set Retry Attempts

```bash
wget --tries=10 https://example.com/file.zip
```

The `--tries` option specifies the number of retry attempts in case of failure.

---

## 10. Skip SSL Certificate Checks

```bash
wget --no-check-certificate https://example.com/file.zip
```

The `--no-check-certificate` option ignores SSL/TLS certificate verification. Use with caution, especially for sensitive downloads.

---

## 11. Download in Background

```bash
wget -b https://example.com/largefile.zip
```

The `-b` option downloads the file in the background. Check the progress in the `wget-log` file.

---

## 12. Use Authentication

### Basic Authentication

```bash
wget --user=username --password=password https://example.com/securefile.zip
```

### Using `.netrc` File

Store credentials in `~/.netrc` and use:

```bash
wget --auth-no-challenge https://example.com/securefile.zip
```

---

## 13. Recursive Download

```bash
wget -r https://example.com/directory/
```

The `-r` option enables recursive downloads, retrieving files and subdirectories up to a certain depth.

---

## 14. Specify a Download Depth

```bash
wget -r -l 2 https://example.com/
```

The `-l` (level) option limits the depth of the recursive download. For example, `-l 2` downloads up to 2 levels deep.

---

## 15. Use a Proxy

### HTTP Proxy

```bash
wget -e use_proxy=yes -e http_proxy=http://proxyserver:port https://example.com/file.zip
```

### SOCKS Proxy

```bash
wget -e use_proxy=yes -e socks_proxy=socks://proxyserver:port https://example.com/file.zip
```

---

These snippets should cover most use cases for `wget`. Experiment with combining options to suit your specific requirements!
