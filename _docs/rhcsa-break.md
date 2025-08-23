---

layout: default
title: "RHEL VM DESTRUCTION"

---

# Table of Contents 
{: .no_toc }

1. TOC 
{:toc}

---

In order to get more practice studying for RHCSA , i've decided to cobble together a few tactics that will force me to stop , think , and fix various things on my VM. I've done this as it's easy to coast along doing the same repetitive tasks while studying hammer commands & snippets. This however is a chance to break outside of the repition and force me to apply the knowledge and fix a very broken VM. This is not for the faint of heart, you have been warned.


## 1. Changed root password to something random

Setup root password as something random , nuff said

## 2. Wiped all configurations and connections in NetworkManager

```bash
sudo rm -rf /etc/NetworkManager/system-connections/*
```

## 3. Corrupted Bootloader entry 

1. Changed default systemd unit to boot into getty.target 

## 4. Disabled sshd daemon 

```bash
systemctl disable sshd
```

## 5. Block ssh in firewall 

```bash
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address=0.0.0.0/0 service name=ssh drop'
firewall-cmd --reload

```

## 6. Masked firewalld systemd unit 

```bash
systemctl mask firewalld.service
```

## 7. Created systemd unit to hog up CPU usage 

```bash
[Unit]
Description=Hog up CPU usage 
StartLimitIntervalSec=0

[Service]
ExecStart=/usr/bin/yes
StandardOutput=null
StandardError=null
Restart=always
RestartSec=0

# Remove caps on resource usage 
CPUQuota=0 
MemoryMax=infinity
MemorySwapMax=infinity
TasksMax=infinity

[Install]
WantedBy=multi-user.target
```

## 8. Changed default UMASK in /etc/login.defs to 000

```bash
UMASK 000 
```

## 9. Symlinked /home to /dev/null 

```bash
sudo ln -s /dev/null /home
```

## 10. Masked network.target 

```bash
systemctl mask network.target
```


## 11. Change default port for httpd to random port 

## 13. Changed attributes on login.defs , hosts , and sudoers file to make it immutable 

```bash 
chattr +i /etc/login.defs
chattr +i /etc/hosts
chattr +i /etc/sudoers
```



## 14. Used script to change selinux type of 20 random files in /etc and /var

Script used 

```bash
#!/usr/bin/env bash
# selinux-chaos.sh
# Randomly change SELinux *type* on random files, with logging & restore.

set -uo pipefail

LOG_DIR="/var/tmp/selinux-chaos"
LOG_FILE="$LOG_DIR/changes-$(date +%Y%m%d-%H%M%S).csv"
DEFAULT_DIRS=("/var/www" "/srv" "/var/log" "/home")
DEFAULT_COUNT=10
DRY_RUN=0

# Curated list of *object* types that won't instantly brick a box.
# Edit to taste for your labs.
SELINUX_TYPES=(
  httpd_sys_content_t
  httpd_sys_rw_content_t
  httpd_log_t
  httpd_var_run_t
  samba_share_t
  var_log_t
  var_t
  user_home_t
  default_t
  mysqld_db_t
  named_cache_t
)

usage() {
  cat <<'EOF'
Usage:
  selinux-chaos.sh break [-n COUNT] [-d DIR ...] [--dry-run]
  selinux-chaos.sh restore --from LOG.csv
  selinux-chaos.sh restore-defaults [-d DIR ...]

Options:
  -n COUNT       Number of files to alter (default: 10)
  -d DIR ...     One or more directories to randomize (default: /var/www /srv /var/log /home)
  --dry-run      Show what would change without applying it
  --from FILE    CSV produced by this script (path,orig_type,new_type,timestamp)

Examples:
  # Break 20 random files under defaults:
  sudo ./selinux-chaos.sh break -n 20

  # Break 5 files only under /var/www and /home:
  sudo ./selinux-chaos.sh break -n 5 -d /var/www /home

  # Restore using a specific log:
  sudo ./selinux-chaos.sh restore --from /var/tmp/selinux-chaos/changes-YYYYMMDD-HHMMSS.csv

  # Nuke custom types back to defaults in target dirs:
  sudo ./selinux-chaos.sh restore-defaults -d /var/www /srv
EOF
}

need_root() {
  if [[ $(id -u) -ne 0 ]]; then
    echo "This script must run as root." >&2
    exit 1
  fi
}

check_selinux_enforcing() {
  if ! command -v getenforce >/dev/null 2>&1; then
    echo "getenforce not found. Is SELinux installed?" >&2
    exit 1
  fi
  mode=$(getenforce)
  if [[ "$mode" != "Enforcing" && "$mode" != "Permissive" ]]; then
    echo "SELinux appears disabled (getenforce=$mode). Enable it for a useful lab." >&2
  fi
}

ensure_tools() {
  for bin in ls chcon restorecon find shuf awk grep sed; do
    command -v "$bin" >/dev/null 2>&1 || { echo "Missing dependency: $bin" >&2; exit 1; }
  done
}

is_safe_path() {
  # Skip dangerous/system locations no matter what.
  case "$1" in
    /proc/*|/sys/*|/dev/*|/run/*|/boot/*|/etc/selinux/*|/usr/lib/selinux/*|/var/lib/selinux/*) return 1 ;;
    *) return 0 ;;
  esac
}

get_type() {
  # Extract the SELinux *type* (3rd field) from ls -Zd
  local f="$1"
  local ctx
  # If file disappears mid-run, return empty
  ctx=$(ls -Zd -- "$f" 2>/dev/null | awk '{print $1}' || true)
  [[ -z "$ctx" ]] && { echo ""; return 0; }
  echo "$ctx" | awk -F: '{print $3}'
}

pick_random_type() {
  local current="$1"
  # Shuffle types until we pick one different from current
  printf "%s\n" "${SELINUX_TYPES[@]}" | shuf | awk -v cur="$current" '$0 != cur {print; exit}'
}

cmd_break() {
  local count="$DEFAULT_COUNT"
  local -a dirs=("${DEFAULT_DIRS[@]}")

  # Parse args
  while [[ $# -gt 0 ]]; do
    case "$1" in
      -n) count="$2"; shift 2 ;;
      -d) shift; dirs=(); while [[ $# -gt 0 && "$1" != -* ]]; do dirs+=("$1"); shift; done ;;
      --dry-run) DRY_RUN=1; shift ;;
      *) echo "Unknown option: $1" >&2; usage; exit 1 ;;
    esac
  done

  [[ "$count" =~ ^[0-9]+$ ]] || { echo "-n COUNT must be a number" >&2; exit 1; }

  # Build candidate list
  mapfile -t candidates < <(
    for d in "${dirs[@]}"; do
      [[ -d "$d" ]] || continue
      is_safe_path "$d" || continue
      # Regular files only; skip sockets/fifos/symlinks
      find "$d" -xdev -type f -readable 2>/dev/null
    done | grep -Ev '^$' | shuf
  )

  if [[ ${#candidates[@]} -eq 0 ]]; then
    echo "No candidate files found in: ${dirs[*]}" >&2
    exit 1
  fi

  mkdir -p "$LOG_DIR"
  echo "#path,orig_type,new_type,timestamp" > "$LOG_FILE"

  local changed=0
  for f in "${candidates[@]}"; do
    is_safe_path "$f" || continue
    orig_type=$(get_type "$f")
    [[ -z "$orig_type" ]] && continue
    new_type=$(pick_random_type "$orig_type")
    [[ -z "$new_type" ]] && continue

    echo "[$((changed+1))/$count] $f  $orig_type -> $new_type"
    echo "$f,$orig_type,$new_type,$(date -Is)" >> "$LOG_FILE"

    if [[ "$DRY_RUN" -eq 0 ]]; then
      if ! chcon -t "$new_type" -- "$f" 2>/dev/null; then
        echo "  ! chcon failed on $f (skipping)" >&2
        # Remove the last log line since it didn’t apply
        sed -i '$d' "$LOG_FILE"
        continue
      fi
    fi

    ((changed++))
    [[ "$changed" -ge "$count" ]] && break
  done

  if [[ "$DRY_RUN" -eq 1 ]]; then
    echo "Dry run complete. No changes applied."
    # Don’t keep a dry-run log
    rm -f "$LOG_FILE"
  else
    echo "Changes logged to: $LOG_FILE"
    echo "Tip: grep your AVCs with 'ausearch -m avc -ts recent' then practice restorecon or booleans."
  fi
}

cmd_restore_from_log() {
  local file=""
  while [[ $# -gt 0 ]]; do
    case "$1" in
      --from) file="$2"; shift 2 ;;
      *) echo "Unknown option: $1" >&2; usage; exit 1 ;;
    esac
  done
  [[ -f "$file" ]] || { echo "Log file not found: $file" >&2; exit 1; }

  echo "Restoring SELinux types from $file ..."
  # Skip comment/header lines
  while IFS=, read -r path orig new ts; do
    [[ "$path" =~ ^# ]] && continue
    [[ -z "$path" || -z "$orig" ]] && continue
    if [[ -e "$path" ]]; then
      echo "chcon -t $orig -- $path"
      chcon -t "$orig" -- "$path" 2>/dev/null || echo "  ! Failed on $path"
    fi
  done < "$file"
  echo "Done."
}

cmd_restore_defaults() {
  local -a dirs=("${DEFAULT_DIRS[@]}")
  while [[ $# -gt 0 ]]; do
    case "$1" in
      -d) shift; dirs=(); while [[ $# -gt 0 && "$1" != -* ]]; do dirs+=("$1"); shift; done ;;
      *) echo "Unknown option: $1" >&2; usage; exit 1 ;;
    esac
  done

  for d in "${dirs[@]}"; do
    [[ -d "$d" ]] || continue
    is_safe_path "$d" || continue
    echo "restorecon -RFv $d"
    restorecon -RFv "$d"
  done
  echo "Defaults restored for target dirs."
}

main() {
  [[ $# -lt 1 ]] && { usage; exit 1; }
  sub="$1"; shift || true

  case "$sub" in
    break)
      need_root
      check_selinux_enforcing
      ensure_tools
      cmd_break "$@"
      ;;
    restore)
      need_root
      ensure_tools
      cmd_restore_from_log "$@"
      ;;
    restore-defaults)
      need_root
      ensure_tools
      cmd_restore_defaults "$@"
      ;;
    -h|--help|help)
      usage
      ;;
    *)
      echo "Unknown subcommand: $sub" >&2
      usage
      exit 1
      ;;
  esac
}

main "$@"

```

Changes are logged in /tmp 


