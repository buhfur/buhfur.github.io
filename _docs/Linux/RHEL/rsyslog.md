---
title: Rsyslog
parent: RHEL
grand_parent: Linux
nav_order: 100
layout: default
---

# Rsyslog: Practical Guide (commands, snippets, tips & tricks)

This guide focuses on **modern RainerScript syntax** (rsyslog v7/v8+). Paths and units are for Linux.

---

## 0) Quick ops

```bash
# install (examples)
# RHEL/CentOS: sudo dnf install rsyslog rsyslog-gnutls rsyslog-relp
# Debian/Ubuntu: sudo apt-get install rsyslog rsyslog-gnutls rsyslog-relp

# service control
sudo systemctl enable --now rsyslog
sudo systemctl status rsyslog
sudo systemctl reload rsyslog   # reload configs in /etc/rsyslog.conf and /etc/rsyslog.d/*.conf
sudo systemctl restart rsyslog

# sanity checks
sudo rsyslogd -N1     # validate configuration and exit
logger -p user.info "hello from $(hostname) via logger"   # send a test message

# read rsyslog’s own logs
journalctl -u rsyslog --no-pager -n 200
```

Default config files:
- `/etc/rsyslog.conf` (main)
- `/etc/rsyslog.d/*.conf` (drop-ins)

---

## 1) Anatomy (inputs → rulesets → actions)

Minimal skeleton (drop into `/etc/rsyslog.d/10-base.conf`):

```conf
# where to keep queue spill files etc.
global(workDirectory="/var/lib/rsyslog")

# inputs (examples)
module(load="imuxsock")        # /dev/log (apps using libc syslog)
module(load="imklog")          # kernel (only on older/non-systemd systems)
# On systemd systems, prefer imjournal OR imuxsock (see §2).

# a ruleset processes messages and runs actions
ruleset(name="default"){  # this ruleset is bound implicitly if not specified
  # basic local log file
  action(type="omfile" file="/var/log/messages")
}
```

**Selector-style** (classic) still works:

```conf
# facility.severity  destination
*.info;mail.none;authpriv.none;cron.none   /var/log/messages
```

But prefer expression filters (see §4).

---

## 2) Journald integration (imjournal vs imuxsock)

Systemd hosts have two common patterns:

**A. Pull from the journal (imjournal) — preserves journald metadata.**
```conf
module(load="imjournal"
       StateFile="/var/lib/rsyslog/imjournal.state"
       Ratelimit.Interval="5"
       Ratelimit.Burst="20000")
```

**B. Take the syslog socket (imuxsock) — fastest, text only.**
```conf
module(load="imuxsock" SysSock.Use="on")
```

**Avoid duplicates:** use **one** of the above patterns. If you use `imjournal` (pull), leave `ForwardToSyslog=no` in `/etc/systemd/journald.conf`. If you use `imuxsock` (push), you may enable `ForwardToSyslog=yes` and **do not** load `imjournal`.

Restart journald after changing its config:
```bash
sudo systemctl restart systemd-journald
```

---

## 3) Read plain files (imfile), including multi‑line

Basic file tailing:
```conf
module(load="imfile")

input(type="imfile"
      File="/var/log/myapp/app.log"
      Tag="myapp"
      addMetadata="on")
```

Multi‑line (e.g., stack traces):
```conf
# Built-ins: readMode="0|1|2"
# or regex boundaries:
input(type="imfile"
      File="/var/log/myapp/app.log"
      Tag="myapp"
      addMetadata="on"
      startmsg.regex="^[0-9]{4}-[0-9]{2}-[0-9]{2}T"   # new record starts at ISO timestamp
      readTimeout="5"
      escapeLF="on")  # flatten embedded newlines as #012
```

Separate severity/facility for a file:
```conf
input(type="imfile" File="/var/log/myapp/audit.log" Tag="myapp_audit"
      Facility="local4" Severity="notice")
```

---

## 4) Filtering & routing (expression filters)

Handy properties: `$msg`, `$programname`, `$hostname`, `$fromhost-ip`, `$syslogseverity` (0–7), `$syslogseverity-text`, `$syslogfacility`, `$app-name` (RFC5424).

Examples:
```conf
# only sshd errors to a file
if ($programname == "sshd" and $syslogseverity <= 3) then {
  action(type="omfile" file="/var/log/sshd-errors.log")
  stop    # prevent further processing
}

# drop noisy health checks
if ($programname == "nginx" and $msg contains "ELB-HealthChecker") then stop

# route kernel warnings and above to console
if ($syslogfacility == 0 and $syslogseverity <= 4) then
  action(type="omusrmsg" users="root")
```

Classic selector quickies:
```conf
authpriv.*                         /var/log/secure
mail.*                             -/var/log/maillog
*.emerg                            :omusrmsg:*     # wall
```

---

## 5) Templates (format or dynamic paths)

### 5.1 Dynamic files per host/service
```conf
# per-host file under /var/log/remote/<host>/syslog.log
template(name="PerHostFile" type="string"
         string="/var/log/remote/%HOSTNAME%/syslog.log")

if $fromhost-ip != "127.0.0.1" then
  action(type="omfile" dynaFile="PerHostFile" createDirs="on")
```

### 5.2 Emit structured JSON
Modern JSON templates avoid manual quoting:
```conf
# flattened JSON
template(name="out_json" type="list" option.jsonf="on") {
  property(outname="@timestamp" name="timereported" dateFormat="rfc3339" format="jsonf")
  property(outname="host"       name="hostname"     format="jsonf")
  property(outname="severity"   name="syslogseverity-text" caseConversion="upper" format="jsonf")
  property(outname="facility"   name="syslogfacility" format="jsonf" dataType="number")
  property(outname="tag"        name="syslogtag"    format="jsonf")
  property(outname="program"    name="programname"  format="jsonf" onEmpty="null")
  property(outname="message"    name="msg"          format="jsonf")
}
```

Use with any action:
```conf
action(type="omfile" file="/var/log/json/syslog.json" template="out_json")
```

For nested JSON keys, use `option.jsonftree="on"` and dotted `outname` (e.g., `event.dataset.name`).

---

## 6) Forwarding logs

### 6.1 TCP (omfwd)
```conf
# basic TCP forward with a protective queue
action(type="omfwd"
       target="logs.example.com" port="514" protocol="tcp"
       # per-action queue (prevents blocking if remote is slow/down)
       queue.type="LinkedList"
       action.resumeRetryCount="-1")
```

### 6.2 TLS over TCP (omfwd + gtls/ossl)

Global TLS (example files, adjust paths):
```conf
# global TLS defaults (choose one driver; ossl or gtls)
global(DefaultNetstreamDriverCAFile="/etc/rsyslog.d/ca.crt")
# optional per-action overrides exist; see docs if mixing destinations.
```

Action with peer validation:
```conf
action(type="omfwd"
       target="logs.example.com" port="6514" protocol="tcp"
       StreamDriver="gtls"                # or "ossl" on newer builds
       StreamDriverMode="1"               # TLS only
       StreamDriverAuthMode="x509/name"   # verify server cert CN/SAN
       StreamDriverPermittedPeers="logs.example.com"
       queue.type="LinkedList" queue.size="100000" action.resumeRetryCount="-1")
```

Quick legacy equivalents (still supported):
```
*.* @@logs.example.com:6514
```

### 6.3 RELP (reliable) — recommended when loss is unacceptable

Client:
```conf
module(load="omrelp")
action(type="omrelp" target="central.example.net" port="2514"
       queue.type="LinkedList" action.resumeRetryCount="-1")
```

Server:
```conf
module(load="imrelp")
input(type="imrelp" port="2514" ruleset="relp_in")
ruleset(name="relp_in") {
  action(type="omfile" file="/var/log/remote/relp.log" createDirs="on")
}
```

---

## 7) Receiving remote logs (server side)

UDP (514) and/or TCP (514):
```conf
module(load="imudp")
input(type="imudp" port="514")

module(load="imtcp")
input(type="imtcp" port="514")
```

TLS listener (TCP 6514) example:
```conf
module(load="imtcp"
       StreamDriver.Name="gtls"
       StreamDriver.Mode="1"             # TLS only
       StreamDriver.Authmode="x509/name")

# per‑port listener
input(type="imtcp" port="6514" ruleset="from_net")
ruleset(name="from_net"){
  action(type="omfile" dynaFile="PerHostFile" createDirs="on")
}
```

Firewall/SELinux hints (RHEL/derivatives):
```bash
sudo firewall-cmd --add-service=syslog --permanent   # UDP 514 (adjust for TCP/6514)
sudo firewall-cmd --add-port=6514/tcp --permanent && sudo firewall-cmd --reload
# If using nonstandard ports, label them:
sudo semanage port -a -t syslogd_port_t -p tcp 6514  # if needed
```

---

## 8) Queues that won’t drop your logs

Action queue: isolate slow/remotes from the main flow.
```conf
# attach to an action (e.g., a forward)
action(type="omfwd" target="logs.example.com" protocol="tcp" port="6514"
       StreamDriver="gtls" StreamDriverMode="1" StreamDriverAuthMode="x509/name"
       queue.type="LinkedList"                # in‑memory linked list
       queue.size="200000"                    # entries
       queue.filename="fwdq"                  # enables disk‑assisted queue (DA)
       queue.maxDiskSpace="2g"                # spill limit
       queue.saveOnShutdown="on"
       queue.highWatermark="180000"           # backpressure
       queue.lowWatermark="50000"
       action.resumeRetryCount="-1")          # keep retrying forever
```

Notes:
- Set `global(workDirectory="/var/lib/rsyslog")` to place queue files.
- Use DA queues for anything remote or slow (DBs, network forwards).
- For bursty inputs, consider `main_queue(...)` as well (advanced).

---

## 9) Useful modules and one‑liners

**impstats** — expose internal counters periodically:
```conf
module(load="impstats" interval="60" severity="7" log.file="/var/log/rsyslog-stats.log")
```

**mmutf8fix** — ensure valid UTF‑8 before forwarding:
```conf
module(load="mmutf8fix")
action(type="mmutf8fix")    # place before outputs that choke on invalid UTF‑8
```

**mmnormalize** — parse unstructured logs via liblognorm (example gist only):
```conf
module(load="mmnormalize")
ruleset(name="parse_app"){
  action(type="mmnormalize" ruleBase="/etc/rsyslog.d/app.rulebase")
  action(type="omfile" file="/var/log/app/parsed.log")
}
```

---

## 10) Troubleshooting checklist

```bash
# 1) Validate config without starting the daemon
sudo rsyslogd -N1

# 2) Reload and watch the unit logs
sudo systemctl reload rsyslog
journalctl -u rsyslog -f

# 3) Generate test events
logger -p local0.warning -t TEST "rsyslog test message"

# 4) Enable debug on demand (temporary)
export RSYSLOG_DEBUG="DebugOnDemand NoStdOut"
export RSYSLOG_DEBUGLOG="/tmp/rsyslog-debug.log"
# trigger debug (toggle) for the running daemon
sudo kill -USR1 "$(cat /var/run/rsyslogd.pid)"

# 5) Watch impstats (if enabled)
tail -F /var/log/rsyslog-stats.log
```

Common gotchas:
- imjournal **and** journald `ForwardToSyslog=yes` together → duplicates. Pick one path (§2).
- TLS connects but still warns: set a CA file and verify peers. Prefer `StreamDriverAuthMode="x509/name"`.
- Forwarding stalls when remote is down → add a **queue** to the action (§8).
- JSON formatting looks hand‑crafted → use `option.jsonf`/`jsonftree` templates (§5.2).
- Multi‑line from files gets split → use `startmsg.regex`/`readMode` and `escapeLF` (§3).

---

## 11) Cheatsheet (facilities & severities)

Facilities (common): `auth`, `authpriv`, `cron`, `daemon`, `kern`, `lpr`, `mail`, `news`, `syslog`, `user`, `uucp`, `local0`…`local7`  
Severities (0–7): `emerg`, `alert`, `crit`, `err`, `warning`, `notice`, `info`, `debug`

Selector examples:
```
*.emerg                            :omusrmsg:*     # broadcast
authpriv.*                         /var/log/secure
mail.*                             -/var/log/maillog
*.info;mail.none;authpriv.none     /var/log/messages
```

---

## 12) Small patterns you’ll reuse

Split nginx access/error to separate files:
```conf
if $programname == "nginx" and ($msg contains "GET " or $msg contains "POST ") then
  action(type="omfile" file="/var/log/nginx/access.parsed")

if $programname == "nginx" and ($msg contains " error " or $syslogseverity <= 3) then
  action(type="omfile" file="/var/log/nginx/error.parsed")
```

Forward only sshd + kernel warnings to SIEM over TLS:
```conf
if ($programname == "sshd" or ($syslogfacility == 0 and $syslogseverity <= 4)) then
  action(type="omfwd"
         target="siem.example.org" port="6514" protocol="tcp"
         StreamDriver="gtls" StreamDriverMode="1" StreamDriverAuthMode="x509/name"
         queue.type="LinkedList" queue.filename="siemq" queue.saveOnShutdown="on")
```

Per‑app JSON to disk:
```conf
if $programname == "myapp" then
  action(type="omfile" file="/var/log/myapp/events.json" template="out_json" createDirs="on")
```

---

### Appendix: quick severity numbers

```
0=emerg 1=alert 2=crit 3=err 4=warning 5=notice 6=info 7=debug
```

---

Happy logging.
