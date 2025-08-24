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

---

# RHCSA Break Script 

I have also generated a script using chatgpt that will break various parts of the system , this script can be fine tuned to either break alot or very little. There is also an option to reverse the damage. See the script below. 


```bash

#!/usr/bin/env bash
# rhcsa-breaker.sh — Intentionally "break" a RHEL 9 VM for RHCSA practice
# Levels: light | medium | heavy
# Always creates backups under /root/rhcsa_breaker_backup/<timestamp>
# Supports: --level <lvl>, --dry-run, --list, --restore <backup_dir>
# Tested on: RHEL/Alma/Rocky 9
set -euo pipefail

LEVEL="${1:-}"
shift || true ||:

DRY_RUN=false
RESTORE_DIR=""
SHOW_LIST=false

# ---------- helpers ----------
log(){ echo "[*] $*"; }
warn(){ echo "[!] $*" >&2; }
die(){ echo "[x] $*" >&2; exit 1; }

require_root(){
  [[ $EUID -eq 0 ]] || die "Run as root."
}

ts(){ date +%Y%m%d-%H%M%S; }

backup_root="/root/rhcsa_breaker_backup"
timestamp="$(ts)"
workdir="$backup_root/$timestamp"
manifest="$workdir/manifest.txt"

ensure_dirs(){
  mkdir -p "$workdir"
  touch "$manifest"
}

backup_file(){
  local f="$1"
  if [[ -e "$f" ]]; then
    local dest="$workdir$(echo "$f" | sed 's#/#_#g')"
    $DRY_RUN || cp -a "$f" "$dest"
    echo "FILE $f -> $dest" >> "$manifest"
  fi
}

backup_cmd_out(){
  local name="$1"; shift
  local dest="$workdir/cmd_${name}.txt"
  $DRY_RUN || ( "$@" > "$dest" 2>&1 || true )
  echo "CMD $name -> $dest : $*" >> "$manifest"
}

record_change(){
  # id | description | hint
  echo "CHANGE|$1|$2|$3" >> "$manifest"
}

apply(){
  if $DRY_RUN; then
    echo "DRYRUN: $*"
  else
    eval "$@"
  fi
}

usage(){
cat <<EOF
Usage:
  $0 --level {light|medium|heavy} [--dry-run]
  $0 --list
  $0 --restore <backup_dir>

Examples:
  $0 --level light
  $0 --level medium --dry-run
  $0 --restore /root/rhcsa_breaker_backup/20250824-120102

Notes:
- Backups & a manifest are stored in $backup_root/<timestamp>.
- Use the manifest for fix hints and to see what changed.
EOF
}

# ---------- parse args ----------
while [[ $# -gt 0 ]]; do
  case "$1" in
    --level) LEVEL="$2"; shift 2;;
    --dry-run) DRY_RUN=true; shift;;
    --restore) RESTORE_DIR="$2"; shift 2;;
    --list) SHOW_LIST=true; shift;;
    -h|--help) usage; exit 0;;
    *) shift;;
  esac
done

list_breaks(){
  cat <<'LIST'
LIGHT (aimed at 15–30 min of fixes):
  - firewalld: drop SSH/Cockpit services in active zone
  - SELinux: relabel wrong context on /var/www/html/index.html
  - SELinux: toggle a boolean (httpd_can_network_connect) to off
  - systemd: make a service fail (create bogus override for rsyslog)
  - Users/Groups: lock a user and break sudo via removed wheel membership of a secondary test user
  - Permissions: remove execute bit on a common tool (/usr/bin/less via per-file ACL)

MEDIUM (45–90 min):
  - fstab: add an entry that fails to mount (nofail) + wrong options
  - Networking: create a bad NM connection profile and set it autoconnect-priority higher
  - systemd: mask a service (chronyd) and stop it
  - firewalld: move interface to the wrong zone & prune services
  - SELinux: mislabel a custom port (bind 8081 as http_port_t then change to ssh_port_t)
  - Permissions/ACL: give odd ACL on /srv/share and remove default perms from a file

HEAVY (multi-hour set; snapshot recommended):
  - systemd: mask multi-user dependencies (disable crond, atd; add bogus drop-in to NetworkManager)
  - PAM: require a module that isn't installed for login shells (but leave console TTY usable)
  - SSH: change port, disable password auth, and restrict to a non-existent AllowUsers
  - firewalld: default zone to drop, remove services from public
  - DNS: point resolvers to invalid servers
  - SELinux: change file context recursively on /var/www/html to a wrong type
  - Users: expire a user's password & lock another
LIST
}

# ---------- individual breakers ----------
break_firewalld_light(){
  log "Adjusting firewalld (drop SSH/Cockpit in active zone)"
  backup_cmd_out "firewalld_list" firewall-cmd --list-all
  local zone
  zone="$(firewall-cmd --get-active-zones | awk 'NR==1{print $1}')"
  [[ -z "$zone" ]] && zone="public"
  apply "firewall-cmd --permanent --zone $zone --remove-service=ssh || true"
  apply "firewall-cmd --permanent --zone $zone --remove-service=cockpit || true"
  apply "firewall-cmd --reload"
  record_change "FIREWALL1" "Removed ssh/cockpit from zone '$zone'." "firewall-cmd --zone $zone --add-service=ssh --permanent; firewall-cmd --reload"
}

break_selinux_file_context_light(){
  log "Mislabel a single web file"
  mkdir -p /var/www/html
  [[ -e /var/www/html/index.html ]] || echo "hello" > /var/www/html/index.html
  backup_cmd_out "semanage_fcontext_list" semanage fcontext -l
  apply "semanage fcontext -a -t net_conf_t '/var/www/html/index.html'"
  apply "restorecon -v /var/www/html/index.html"  # will set wrong due to rule
  record_change "SELINUX1" "Set fcontext of index.html to net_conf_t." "semanage fcontext -d '/var/www/html/index.html'; restorecon -v /var/www/html/index.html"
}

break_selinux_boolean_light(){
  log "Toggle SELinux boolean httpd_can_network_connect OFF"
  backup_cmd_out "getsebool_httpd_net" getsebool httpd_can_network_connect
  apply "setsebool -P httpd_can_network_connect off"
  record_change "SELINUX2" "httpd_can_network_connect=off." "setsebool -P httpd_can_network_connect on"
}

break_systemd_override_light(){
  log "Break rsyslog via bogus override"
  mkdir -p /etc/systemd/system/rsyslog.service.d
  backup_file /etc/systemd/system/rsyslog.service.d/override.conf
  cat > /tmp/override.conf <<'EOF'
[Service]
ExecStart=
ExecStart=/bin/false
EOF
  apply "cp /tmp/override.conf /etc/systemd/system/rsyslog.service.d/override.conf"
  apply "systemctl daemon-reload"
  apply "systemctl restart rsyslog || true"
  record_change "SYSTEMD1" "rsyslog override forces failure." "rm -f /etc/systemd/system/rsyslog.service.d/override.conf; systemctl daemon-reload; systemctl restart rsyslog"
}

break_user_group_light(){
  log "Create test user 'labuser', remove wheel, lock account"
  id labuser &>/dev/null || apply "useradd -m labuser"
  backup_cmd_out "groups_labuser" id labuser
  apply "gpasswd -d labuser wheel || true"
  apply "passwd -l labuser"
  record_change "USER1" "labuser removed from wheel and locked." "usermod -aG wheel labuser; passwd -u labuser"
}

break_acl_light(){
  log "Remove execute on /usr/bin/less via ACL (does not touch base perms)"
  backup_cmd_out "getfacl_less" getfacl /usr/bin/less
  apply "setfacl -m u::r-- /usr/bin/less || true"
  record_change "ACL1" "less made non-executable via ACL." "setfacl -b /usr/bin/less"
}

break_fstab_medium(){
  log "Add failing fstab entry with nofail (practice mount troubleshooting)"
  backup_file /etc/fstab
  echo "# RHCSA-BREAKER test" >> /etc/fstab
  echo "UUID=0000-FAKE-UUID  /mnt/bad  xfs  defaults,nofail  0 0" | tee -a /etc/fstab >/dev/null
  apply "mkdir -p /mnt/bad"
  record_change "FSTAB1" "Added bad fstab entry." "edit /etc/fstab to remove/fix the bad line; then systemctl daemon-reload; mount -a"
}

break_nm_medium(){
  log "Create a bad NetworkManager profile with higher priority"
  backup_cmd_out "nmcli_con_show" nmcli -t -f NAME,UUID,DEVICE con show
  apply "nmcli con add type ethernet ifname eth0 con-name bad-eth0 || true"
  apply "nmcli con mod bad-eth0 ipv4.method manual ipv4.addresses 192.0.2.10/24 ipv4.gateway 192.0.2.1 ipv4.dns '203.0.113.53' autoconnect yes connection.autoconnect-priority 10 || true"
  record_change "NET1" "Added bad-eth0 NM profile with autoconnect priority." "nmcli con delete bad-eth0 OR fix the IP/gw/dns; check 'nmcli con up <good>' and priorities"
}

break_systemd_mask_medium(){
  log "Mask chronyd"
  backup_cmd_out "systemctl_status_chronyd" systemctl status chronyd || true
  apply "systemctl stop chronyd || true"
  apply "systemctl mask chronyd"
  record_change "SYSTEMD2" "chronyd masked." "systemctl unmask chronyd; systemctl enable --now chronyd"
}

break_firewalld_zone_medium(){
  log "Move interface to 'work' zone and prune services"
  local ifc; ifc="$(nmcli -t -f DEVICE connection show --active | head -n1)"
  [[ -z "$ifc" ]] && ifc="$(nmcli -t -f DEVICE dev status | awk -F: 'NR==2{print $1}')"
  [[ -z "$ifc" ]] && ifc="eth0"
  apply "firewall-cmd --permanent --zone=work --add-interface=$ifc"
  apply "firewall-cmd --permanent --zone=public --remove-interface=$ifc || true"
  apply "firewall-cmd --permanent --zone=work --remove-service=ssh || true"
  apply "firewall-cmd --reload"
  record_change "FIREWALL2" "Interface $ifc moved to 'work' and ssh removed." "firewall-cmd --permanent --zone=public --add-interface=$ifc; firewall-cmd --permanent --zone=public --add-service=ssh; firewall-cmd --reload"
}

break_selinux_port_medium(){
  log "Mislabel a port: set 8081 as ssh_port_t"
  backup_cmd_out "semanage_port_list" semanage port -l
  apply "semanage port -a -t ssh_port_t -p tcp 8081 || semanage port -m -t ssh_port_t -p tcp 8081"
  record_change "SELINUX3" "8081 labeled as ssh_port_t." "semanage port -d -t ssh_port_t -p tcp 8081 OR set to correct type; semanage port -l | grep 8081"
}

break_acl_medium(){
  log "Odd ACL on /srv/share"
  apply "mkdir -p /srv/share"
  backup_cmd_out "getfacl_srv_share" getfacl -p /srv/share
  apply "setfacl -m u:labuser:--- /srv/share"
  record_change "ACL2" "Deny labuser on /srv/share via ACL." "setfacl -b /srv/share OR setfacl -x u:labuser /srv/share"
}

# HEAVY breakers
break_systemd_heavy(){
  log "Systemd heavy: disable timers/services and add bad drop-in"
  backup_cmd_out "systemctl_list" systemctl list-unit-files --type=service --no-pager
  apply "systemctl disable --now crond || true"
  apply "systemctl disable --now atd || true"
  mkdir -p /etc/systemd/system/NetworkManager.service.d
  backup_file /etc/systemd/system/NetworkManager.service.d/zz-bad.conf
  cat > /tmp/zz-bad.conf <<'EOF'
[Service]
ExecStartPost=/bin/false
EOF
  apply "cp /tmp/zz-bad.conf /etc/systemd/system/NetworkManager.service.d/zz-bad.conf"
  apply "systemctl daemon-reload"
  apply "systemctl restart NetworkManager || true"
  record_change "SYSTEMD3" "Disabled crond/atd; bad NM drop-in." "enable/start crond & atd; remove drop-in; daemon-reload; restart NetworkManager"
}

break_pam_heavy(){
  log "PAM heavy: add non-existent module to system-auth (TTY still usable)"
  backup_file /etc/pam.d/system-auth
  apply "sed -i '1iauth    required    pam_doesnotexist.so' /etc/pam.d/system-auth"
  record_change "PAM1" "Added bad module to /etc/pam.d/system-auth." "edit /etc/pam.d/system-auth to remove the line; run authconfig/realmd checks; test with 'su -'"
}

break_sshd_heavy(){
  log "SSHD heavy: change port, disable PasswordAuth, bad AllowUsers"
  backup_file /etc/ssh/sshd_config
  apply "sed -i 's/^#\?Port .*/Port 22222/' /etc/ssh/sshd_config || echo 'Port 22222' >> /etc/ssh/sshd_config"
  apply "sed -i 's/^#\?PasswordAuthentication .*/PasswordAuthentication no/' /etc/ssh/sshd_config || echo 'PasswordAuthentication no' >> /etc/ssh/sshd_config"
  apply "echo 'AllowUsers nobody' >> /etc/ssh/sshd_config"
  apply "systemctl restart sshd || true"
  record_change "SSH1" "Changed SSH port to 22222; disabled password auth; AllowUsers=nobody." "fix /etc/ssh/sshd_config (Port 22, remove AllowUsers or set correctly, enable PasswordAuthentication if needed); restart sshd; adjust firewalld"
}

break_firewalld_heavy(){
  log "Firewalld heavy: default zone drop, remove services"
  backup_cmd_out "firewalld_zones" firewall-cmd --get-zones
  apply "firewall-cmd --set-default-zone=drop"
  apply "firewall-cmd --permanent --zone=public --remove-service=ssh || true"
  apply "firewall-cmd --reload"
  record_change "FIREWALL3" "Default zone=drop; ssh removed from public." "firewall-cmd --set-default-zone=public; firewall-cmd --permanent --zone=public --add-service=ssh; firewall-cmd --reload"
}

break_dns_heavy(){
  log "DNS heavy: point resolv.conf to invalid resolvers (NetworkManager-managed)"
  backup_file /etc/NetworkManager/conf.d/99-bad-dns.conf
  mkdir -p /etc/NetworkManager/conf.d
  cat > /tmp/99-bad-dns.conf <<'EOF'
[main]
dns=none
[global-dns-domain-*]
servers=203.0.113.253,198.51.100.253
EOF
  apply "cp /tmp/99-bad-dns.conf /etc/NetworkManager/conf.d/99-bad-dns.conf"
  apply "systemctl reload NetworkManager || systemctl restart NetworkManager || true"
  record_change "DNS1" "Invalid DNS via NM conf." "remove /etc/NetworkManager/conf.d/99-bad-dns.conf; reload/restart NetworkManager"
}

break_selinux_heavy(){
  log "SELinux heavy: mislabel entire web root"
  backup_cmd_out "ls_lZ_www" ls -lZ /var/www/html || true
  mkdir -p /var/www/html
  echo "site" > /var/www/html/index.html
  apply "semanage fcontext -a -t var_log_t '/var/www/html(/.*)?'"
  apply "restorecon -Rv /var/www/html"
  record_change "SELINUX4" "Mislabel /var/www/html as var_log_t." "semanage fcontext -d '/var/www/html(/.*)?'; restorecon -Rv /var/www/html"
}

break_users_heavy(){
  log "Users heavy: expire one, lock another"
  id alice &>/dev/null || apply "useradd -m alice"
  id bob &>/dev/null || apply "useradd -m bob"
  apply "chage -E 0 alice"
  apply "passwd -l bob"
  record_change "USER2" "alice expired, bob locked." "chage -E -1 alice; passwd -u bob"
}

# ---------- restore ----------
restore_from(){
  local dir="$1"
  [[ -d "$dir" ]] || die "Backup dir not found: $dir"
  warn "Restoring files from $dir and attempting to revert changes…"
  # Restore files recorded as FILE in manifest
  local man="$dir/manifest.txt"
  [[ -f "$man" ]] || die "Manifest not found in backup dir."
  while IFS= read -r line; do
    if [[ "$line" == FILE* ]]; then
      local src dest
      src="$(echo "$line" | awk '{print $2}')"
      dest="$dir$(echo "$src" | sed 's#/#_#g')"
      if [[ -e "$dest" ]]; then
        log "Restore $src"
        cp -a "$dest" "$src"
      fi
    fi
  done < "$man"
  # Attempt generic reverts
  systemctl daemon-reload || true
  firewall-cmd --reload || true
  restorecon -Rv /var/www/html || true
  log "Done. You may still need to manually undo some changes (see hints in manifest)."
}

# ---------- main ----------
require_root

if $SHOW_LIST; then
  list_breaks
  exit 0
fi

if [[ -n "$RESTORE_DIR" ]]; then
  restore_from "$RESTORE_DIR"
  exit 0
fi

[[ -n "$LEVEL" ]] || { usage; exit 1; }
case "$LEVEL" in
  light|medium|heavy) ;;
  *) die "Unknown level: $LEVEL" ;;
esac

ensure_dirs

log "Backups & manifest: $workdir"
$DRY_RUN && warn "DRY-RUN mode: showing actions only."

# Always capture some baseline info
backup_cmd_out "rpm_list_core" rpm -qa | sort
backup_cmd_out "nmcli_summary" nmcli -t -f DEVICE,STATE,CONNECTION dev status
backup_cmd_out "systemctl_failed" systemctl --failed || true

if [[ "$LEVEL" == "light" ]]; then
  break_firewalld_light
  break_selinux_file_context_light
  break_selinux_boolean_light
  break_systemd_override_light
  break_user_group_light
  break_acl_light
elif [[ "$LEVEL" == "medium" ]]; then
  break_fstab_medium
  break_nm_medium
  break_systemd_mask_medium
  break_firewalld_zone_medium
  break_selinux_port_medium
  break_acl_medium
else # heavy
  break_systemd_heavy
  break_pam_heavy
  break_sshd_heavy
  break_firewalld_heavy
  break_dns_heavy
  break_selinux_heavy
  break_users_heavy
fi

log "Done. Review manifest for hints: $manifest"
echo
echo "=== HINTS (from manifest) ==="
awk -F'|' '/^CHANGE\|/ {printf "- %s: %s\n  Fix: %s\n", $2, $3, $4}' "$manifest" || true

```

