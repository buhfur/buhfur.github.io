---
title: Journald
parent: RHEL
grand_parent: Linux
nav_order: 90
layout: default
---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}


# `systemd-journald` / `journalctl` Guide

This guide focuses on practical commands, helpful snippets, and tips & tricks for working with the systemd journal on Linux (RHEL/Alma, Fedora, Debian/Ubuntu, Arch, etc.). It assumes `systemd` is the init system.

---

## Quick Start: Most-Used Commands

```bash
# See everything from the current boot (newest last)
journalctl -b

# Show logs for a systemd unit (service) since last boot
journalctl -u sshd.service -b

# Show only errors for current boot
journalctl -p err -b

# Follow logs (tail) system-wide, like `tail -f`
journalctl -f

# Follow logs for a unit with 200 recent lines, newest first
journalctl -u nginx.service -n 200 -f -r

# Kernel messages from current boot
journalctl -k -b

# Show last 50 entries in reverse, no pager
journalctl -n 50 -r --no-pager

# Grep-like filtering (regex)
journalctl -g 'connection (reset|closed)'

# Between two times
journalctl --since "2025-10-03 14:00" --until "2025-10-03 18:00"

# Human-friendly times
journalctl --since "yesterday" --until "today 06:00"

# Disk usage summary for the journal
journalctl --disk-usage
```

---

## Priority Filtering

`-p` filters by syslog priority. Range syntax is allowed (e.g., `-p warning..emerg`).

Common priorities:
- `emerg=0`, `alert=1`, `crit=2`, `err=3`, `warning=4`, `notice=5`, `info=6`, `debug=7`

Examples:
```bash
# Everything warning and worse (<= warning)
journalctl -p warning -b

# Only errors and critical (err..crit) from a unit
journalctl -u httpd.service -p err..crit -b
```

---

## Unit-Scoped Views

```bash
# Specific unit
journalctl -u firewalld.service

# All units matching a glob
journalctl -u "nginx*" -b

# Systemd unit + time window + priority
journalctl -u keepalived.service -S -2h -p warning
```

Tips:
- Use `systemctl status <unit>` as a quick summary; it shows recent journal lines.
- Services that log to stdout/stderr end up in the journal when `StandardOutput=journal` or by default on many distros.

---

## Boot Navigation

```bash
# List boots with indices and boot IDs
journalctl --list-boots

# Previous boot
journalctl -b -1

# Two boots ago, errors only
journalctl -b -2 -p err
```

---

## Time Windows

```bash
# Since/Until absolute
journalctl --since "2025-10-01 00:00" --until "2025-10-04 23:59"

# Relative
journalctl -S -30m         # last 30 minutes
journalctl -S -1h          # last hour
journalctl -S yesterday    # since yesterday
```

Notes:
- `--since` has a short form `-S`. `--until` has `-U`.
- Accepts many natural-language forms (`today`, `yesterday`, `1 hour ago`).

---

## Output Formatting

```bash
# Raw message only (great for grep or quick reading)
journalctl -o cat

# JSON (one object per line), good for jq
journalctl -o json

# Pretty JSON
journalctl -o json-pretty

# ISO timestamps
journalctl -o short-iso

# Show explanatory help texts if available (message catalog)
journalctl -x
```

Handy with jq:
```bash
journalctl -u sshd -o json | jq -r '.[\"__REALTIME_TIMESTAMP\", \"MESSAGE\"]'
```

---

## Grep and Structured Field Matching

Regex search:
```bash
journalctl -g 'timeout|reset|unreachable'
# Case-insensitive
journalctl --case-insensitive -g 'failed to start'
```

Field filters (exact match):
```bash
# By systemd unit (same as -u, but explicit field)
journalctl _SYSTEMD_UNIT=sshd.service

# By process executable path
journalctl _EXE=/usr/sbin/sshd

# By command name
journalctl _COMM=sshd

# By UID / GID
journalctl _UID=0
journalctl _GID=0

# By PID
journalctl _PID=1234

# By syslog identifier (traditional applications)
journalctl SYSLOG_IDENTIFIER=cron
```

Common fields to know:
- `_SYSTEMD_UNIT`, `_PID`, `_UID`, `_GID`, `_COMM`, `_EXE`, `SYSLOG_IDENTIFIER`, `MESSAGE_ID`, `PRIORITY`, `MESSAGE`

Tip: Field matches are fast and avoid false positives relative to regex text search.

---

## Following, Paging, and Order

```bash
# Follow (tail) with reverse order (newest first), 100 lines
journalctl -n 100 -r -f

# Disable the pager globally for this invocation
journalctl --no-pager

# Start at end of journal
journalctl -e
```

---

## Cursors: Resume Where You Left Off

```bash
# Show last line and print the cursor
journalctl -n 1 --show-cursor

# Tail after a known cursor (e.g., from a previous run)
journalctl --after-cursor "s=deadbeef..."
```

This is ideal for scripts that need to resume incremental log processing.

---

## Exporting and Archiving

```bash
# Save recent service logs to a file with ISO timestamps
journalctl -u nginx -S -2h -o short-iso > nginx-last-2h.log

# Save as JSON for machine processing
journalctl -u sshd -S yesterday -o json > sshd.json

# Convert a journal directory to text
journalctl -D /var/log/journal -o short-iso > all.txt
```
To convert binary journal files later, just point `journalctl -D /path/to/journal`.

---

## Disk Usage, Rotation, and Vacuuming

Inspect usage:
```bash
journalctl --disk-usage
```

Rotate and vacuum:
```bash
# Force a rotation now (starts a new file)
journalctl --rotate

# Vacuum by keeping total size under 1G
journalctl --vacuum-size=1G

# Vacuum to keep only the last 30 days
journalctl --vacuum-time=30d

# Keep at most 20 files per journal namespace
journalctl --vacuum-files=20
```

These operations act on the currently selected journal directory (system, runtime, or a `-D` target).

---

## Persistent Logging (Survives Reboots)

On many distros, journald defaults to volatile storage in `/run/log/journal`. To make logs persist across reboots:

```bash
sudo mkdir -p /var/log/journal
sudo systemd-tmpfiles --create --prefix /var/log/journal
sudo systemctl restart systemd-journald
# Or:
sudo journalctl --flush
```

Verify:
```bash
journalctl --disk-usage
test -d /var/log/journal && echo "Persistent OK" || echo "Volatile"
```

---

## Configuration Basics: `/etc/systemd/journald.conf`

Create a drop-in to avoid editing the main file:

```bash
sudo mkdir -p /etc/systemd/journald.conf.d/
sudo tee /etc/systemd/journald.conf.d/10-persistent-and-limits.conf >/dev/null <<'EOF'
[Journal]
# Storage can be auto, persistent, volatile, none
Storage=persistent

# Space limits for the system journal (persistent)
SystemMaxUse=2G
SystemKeepFree=500M
SystemMaxFileSize=200M

# Space limits for the runtime journal (in /run)
RuntimeMaxUse=256M
RuntimeKeepFree=64M
RuntimeMaxFileSize=64M

# Rate limiting
RateLimitIntervalSec=30s
RateLimitBurst=2000

# Forwarding (optional, if you run rsyslog/syslog-ng)
ForwardToSyslog=yes
EOF

sudo systemctl restart systemd-journald
```

Notes:
- Other useful keys: `Compress=`, `Seal=`, `SyncIntervalSec=`, `ForwardToKMsg=`, `ForwardToConsole=`, `TTYPath=`
- Some options may vary by systemd version. Check `man journald.conf` on your system.

---

## Access Control

Reading the system journal usually requires root or membership in a group such as `systemd-journal` or `adm` (varies by distro).

```bash
# Add your user to the group that can read the journal
sudo usermod -aG systemd-journal "$USER"
# Or on Debian/Ubuntu:
sudo usermod -aG adm "$USER"
```

Log out and back in to apply group changes.

---

## Kernel and Early-Boot Messages

```bash
# Kernel-only
journalctl -k

# Early boot (initramfs) often ends up in the journal when dracut/systemd-boot logs are enabled
journalctl -b -0 -k
```

---

## Coredumps (Bonus)

`coredumpctl` reads crash dumps stored via journald integration.

```bash
# List recent crashes
coredumpctl list

# Show details of the latest crash
coredumpctl info

# Extract a core for analysis
coredumpctl dump > core.latest

# Launch gdb on the most recent coredump for a program
coredumpctl gdb /usr/bin/myapp
```

If you do not need coredumps, consider setting limits or configuring `coredump.conf` to restrict disk usage.

---

## Logging From Scripts

Use `systemd-cat` to send messages to the journal with a custom tag and priority:

```bash
# Tag "myscript", default priority info
echo "Job started" | systemd-cat -t myscript

# Tag and priority (debug, info, notice, warning, err, crit, alert, emerg)
echo "An error happened" | systemd-cat -t myscript -p err

# Wrap a command so its stdout/stderr go to the journal
systemd-cat -t backup-job -p info rsync -a /data /backup
```

Later:
```bash
journalctl SYSLOG_IDENTIFIER=myscript -S -1h -o short-iso
```

---

## Remote Journal (Optional)

If available on your distro, `systemd-journal-upload` and `systemd-journal-remote` can ship and receive journals.

Sender (upload client):
```bash
sudo systemctl enable --now systemd-journal-upload.service
# Configure /etc/systemd/journal-upload.conf (URL, certs)
```

Receiver:
```bash
sudo systemctl enable --now systemd-journal-remote.socket
# Journals typically under /var/log/journal/remote/
```

For cross-distro deployments, traditional syslog forwarders (rsyslog, syslog-ng) may be simpler.

---

## Troubleshooting & Verification

```bash
# Verify journal files
journalctl --verify

# Show the journal directory currently in use
journalctl -D /var/log/journal --disk-usage
journalctl -D /run/log/journal --disk-usage

# If you changed Storage= and want to move runtime to persistent
journalctl --flush
```

If `journalctl` is slow:
- Prefer structured field filters instead of regex when possible.
- Restrict by time window or unit.
- Avoid pretty JSON during heavy scans; use plain text or `-o json` with downstream tools.

---

## Handy Aliases & Functions (drop into your shell rc)

```bash
# Tail a unit
alias jtf='journalctl -f -o short-iso'

junit() {
  # junit nginx [lines]
  local unit="$1"; local n="${2:-200}"
  journalctl -u "$unit" -n "$n" -r --no-pager
}

# Errors from last boot
alias jerr='journalctl -p err -b'

# Grep journal in the last hour
jgrep() {
  # jgrep "pattern" [unit]
  local pat="$1"; local unit="$2"
  if [ -n "$unit" ]; then
    journalctl -u "$unit" -S -1h -g "$pat"
  else
    journalctl -S -1h -g "$pat"
  fi
}

# Export a unit's last boot logs with ISO timestamps
jexport() {
  # jexport nginx /tmp/nginx.log
  local unit="$1"; local out="$2"
  journalctl -u "$unit" -b -o short-iso --no-pager > "$out"
}
```

---

## Minimal Cheat Sheet

```text
journalctl -b                       # current boot
journalctl --list-boots             # index boots
journalctl -u NAME.service          # by unit
journalctl -p warning               # by priority
journalctl -S -1h                   # last hour
journalctl --since "yesterday"      # human time
journalctl -k                       # kernel only
journalctl -f                       # follow
journalctl -n 200 -r                # last 200, newest first
journalctl -o cat                   # message only
journalctl -g 'regex'               # regex search
journalctl _PID=1234                # by field
journalctl --rotate                 # rotate now
journalctl --vacuum-time=30d        # vacuum retention
journalctl --vacuum-size=1G         # bound size
```

---

## Man Pages Worth Reading

- `man journalctl`
- `man systemd.journal-fields`
- `man journald.conf`
- `man coredumpctl`
- `man systemd-cat`
