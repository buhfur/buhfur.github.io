---
title: Proxmox Docker containers in LXC's 
parent: Misc
grand_parent: Linux
nav_order: 140
layout: default
---
# Table of Contents 
{: .no_toc }

1. TOC
{:toc}

---

# Enabling FUSE Mounts for Docker Containers Inside a Proxmox LXC

This guide explains how to enable FUSE support for Docker containers running inside a Proxmox LXC container.

## Prerequisites

- Proxmox VE host
- LXC container (unprivileged recommended)
- Docker installed inside the LXC

---

# 1. Stop the LXC Container

```bash
pct stop <CTID>
```

Replace `<CTID>` with your container ID.

---

# 2. Enable Required LXC Features

Enable Docker nesting and keyctl support:

```bash
pct set <CTID> -features nesting=1,keyctl=1
```

Verify the configuration:

```bash
pct config <CTID>
```

Expected output:

```text
features: keyctl=1,nesting=1
```

---

# 3. Allow Access to the FUSE Device

Edit the container configuration:

```bash
nano /etc/pve/lxc/<CTID>.conf
```

Add the following lines if they are not already present:

```ini
lxc.cgroup2.devices.allow: c 10:229 rwm
lxc.mount.entry: /dev/fuse dev/fuse none bind,create=file
```

If the `features` line is missing, also add:

```ini
features: nesting=1,keyctl=1
```

Save the file and exit.

---

# 4. Start the Container

```bash
pct start <CTID>
```

---

# 5. Verify FUSE Is Available

Inside the LXC container, run:

```bash
ls -l /dev/fuse
```

Expected output:

```text
crw-rw-rw- 1 root root 10,229 ...
```

If `/dev/fuse` does not exist, verify the LXC configuration and restart the container.

---

# 6. Install FUSE

### Debian / Ubuntu

```bash
apt update
apt install -y fuse3
```

Verify installation:

```bash
fusermount3 --version
```

---

# 7. Allow Docker Containers to Access FUSE

## Docker CLI

```bash
docker run \
--device /dev/fuse \
--cap-add SYS_ADMIN \
your-image
```

Some FUSE-based applications also require a relaxed seccomp profile:

```bash
docker run \
   --device /dev/fuse \
   --cap-add SYS_ADMIN \
   --security-opt seccomp=unconfined \
   your-image
```

---

## Docker Compose

```yaml
services:
    app:
        image: your-image

        devices:
            - /dev/fuse:/dev/fuse

        cap_add:
            - SYS_ADMIN

        security_opt:
            - seccomp=unconfined
```

                                    ---

# 8. Verify FUSE Inside the Docker Container

Start a shell inside the container:

```bash
docker exec -it <container_name> sh
```

Verify the device exists:

```bash
ls -l /dev/fuse
```

Expected output:

```text
crw-rw-rw- 1 root root 10,229 ...
```

---

# Troubleshooting

## `/dev/fuse` does not exist

- Verify the following lines exist in `/etc/pve/lxc/<CTID>.conf`:

```ini
lxc.cgroup2.devices.allow: c 10:229 rwm
lxc.mount.entry: /dev/fuse dev/fuse none bind,create=file
```

- Restart the LXC after making changes.

---

## "Operation not permitted"

Ensure the Docker container was started with:

```text
--device /dev/fuse
--cap-add SYS_ADMIN
```

Some applications additionally require:

```text
--security-opt seccomp=unconfined
```

---

## Docker cannot start

Verify the LXC has nesting and keyctl enabled:

```bash
pct config <CTID>
```

Expected:

```text
features: keyctl=1,nesting=1
```

---

# Security Considerations

- Use **unprivileged** LXC containers whenever possible.
- Grant `SYS_ADMIN` only to containers that require FUSE functionality.
- Only expose `/dev/fuse` to trusted containers.
- Avoid using `seccomp=unconfined` unless it is required by the application.
- Review the documentation for the specific FUSE-based application to determine its minimum required privileges.

---

# Summary

| Component | Purpose |
|----------|---------|
| `nesting=1` | Enables nested container functionality within the LXC |
| `keyctl=1` | Required for Docker in unprivileged LXC containers |
| `lxc.cgroup2.devices.allow` | Grants the LXC permission to access the FUSE device |
| `lxc.mount.entry` | Makes `/dev/fuse` available inside the LXC |
| `--device /dev/fuse` | Passes the FUSE device into the Docker container |
| `--cap-add SYS_ADMIN` | Grants the capabilities required by most FUSE applications |
| `seccomp=unconfined` | May be required by some FUSE-based applications |
