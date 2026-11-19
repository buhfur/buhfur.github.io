---
title: Compression
parent: Utils
grand_parent: Linux
nav_order: 90
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC
{:toc}

---

# Game File Compression on RHEL 9

## 1. Install Compression Tools

### Install Zstandard and File Inspection Utilities

```bash
sudo dnf install zstd file vim-common
```

### Verify xxd Installation

```bash
command -v xxd
```

### Enable RHEL CodeReady Builder Repository

```bash
sudo subscription-manager repos \
  --enable "codeready-builder-for-rhel-9-$(arch)-rpms"
```

### Enable EPEL Repository

```bash
sudo dnf install \
  https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
```

### Install 7-Zip

```bash
sudo dnf install 7zip-standalone
```

### Verify 7-Zip Installation

```bash
command -v 7zz || command -v 7z
7zz --help
```

---


## 2. Test File Compressibility

### Compress a 1 GiB Sample

```bash
head -c 1G game.nup | zstd -15 -T0 -o sample.zst
```

### Check Sample Size

```bash
ls -lh sample.zst
```

### Verify Sample Integrity

```bash
zstd -t sample.zst
```

### Remove Temporary Sample

```bash
rm -- sample.zst
```

---

## 4. Compress Using Zstandard

### Recommended Compression — Level 15

```bash
zstd -15 -T0 -k game.nup -o game.nup.zst
```

### Fast Compression — Level 5

```bash
zstd -5 -T0 -k game.nup -o game-fast.nup.zst
```

### High Compression — Level 19

```bash
zstd -19 -T0 -k game.nup -o game-max.nup.zst
```

### Compress an Entire Directory

```bash
tar -I 'zstd -15 -T0' -cvf GameFolder.tar.zst GameFolder/
```

---

## 5. Compress Using 7-Zip / LZMA2

### Maximum Compression — 128 MiB Dictionary

```bash
7zz a -t7z \
  -m0=lzma2 \
  -mx=9 \
  -md=128m \
  -ms=on \
  -mmt=on \
  game.7z game.nup
```

### Compress an Entire Directory

```bash
7zz a -t7z \
  -m0=lzma2 \
  -mx=9 \
  -md=128m \
  -ms=on \
  -mmt=on \
  GameFolder.7z GameFolder/
```

### Higher Compression Dictionary — 256 MiB

```bash
7zz a -t7z \
  -m0=lzma2 \
  -mx=9 \
  -md=256m \
  -ms=on \
  -mmt=on \
  game-256m.7z game.nup
```

---

## 6. Compare Compression Results

### Compare File Sizes

```bash
ls -lh game.nup game.nup.zst game.7z
```

### Display Exact File Sizes

```bash
stat -c '%n %s bytes' game.nup game.nup.zst game.7z
```

### Calculate Compression Ratio

```bash
original=$(stat -c %s game.nup)
compressed=$(stat -c %s game.nup.zst)

awk -v o="$original" -v c="$compressed" \
  'BEGIN {
    printf "Compression ratio: %.2f:1\n", o/c
    printf "Space saved: %.2f%%\n", (1-c/o)*100
  }'
```

---

## 7. Verify Archive Integrity

### Test Zstandard Archive

```bash
zstd -t game.nup.zst
```

### Test 7-Zip Archive

```bash
7zz t game.7z
```

### Inspect 7-Zip Archive Metadata

```bash
7zz l -slt game.7z
```

### Test Compressed tar Archive

```bash
zstd -t GameFolder.tar.zst
```

### Compare tar Archive with Original Directory

```bash
tar -I zstd -df GameFolder.tar.zst
```

---

## 8. Verify File Contents Using SHA-256

### Create Extraction Directory

```bash
mkdir -p extracted
```

### Decompress Zstandard Archive

```bash
zstd -d -c game.nup.zst > extracted/game.nup
```

### Compare SHA-256 Checksums

```bash
sha256sum game.nup extracted/game.nup
```

### Extract 7-Zip Archive for Verification

```bash
7zz x game.7z -o./extracted-7z
```

### Compare 7-Zip Extraction Checksums

```bash
sha256sum game.nup extracted-7z/game.nup
```

---

## 9. Extract Compressed Archives

### Extract Zstandard File

```bash
zstd -d -c game.nup.zst > game-restored.nup
```

### Extract 7-Zip Archive

```bash
7zz x game.7z -o./restored
```

### Extract tar.zst Archive

```bash
tar -I zstd -xvf GameFolder.tar.zst
```

---

## 10. Recommended Compression Commands

### Fast Compression

```bash
zstd -5 -T0 -k game.nup -o game-fast.nup.zst
```

### Balanced Compression

```bash
zstd -15 -T0 -k game.nup -o game.nup.zst
```

### High Compression

```bash
zstd -19 -T0 -k game.nup -o game-max.nup.zst
```

### Maximum 7-Zip Compression Effort

```bash
7zz a -t7z \
  -m0=lzma2 \
  -mx=9 \
  -md=128m \
  -ms=on \
  -mmt=on \
  game.7z game.nup
```
