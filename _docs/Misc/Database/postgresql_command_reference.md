---
title: Postgres Snippets 
parent: Database
grand_parent: Misc
nav_order: 70
layout: default
---

# Table of Contents 
{:.no_toc}

1. TOC 
{:toc}

--- 

# PostgreSQL Administration Command Reference

A practical reference for setting up PostgreSQL, creating databases and users, inspecting schemas, modifying databases, and performing backups and restores.

> Replace example values such as `hydra_auctions`, `hydra_user`, `localhost`, and file paths before running commands.

---

## 1. PostgreSQL Installation

### RHEL / Rocky Linux / AlmaLinux / Fedora

Install the PostgreSQL client and server packages:

```bash
sudo dnf install postgresql postgresql-server
```

Initialize the database cluster:

```bash
sudo postgresql-setup --initdb
```

Enable and start PostgreSQL:

```bash
sudo systemctl enable --now postgresql
```

Check service status:

```bash
sudo systemctl status postgresql
```

Check installed versions:

```bash
psql --version
postgres --version
pg_dump --version
```

### Debian / Ubuntu

```bash
sudo apt update
sudo apt install postgresql postgresql-client
sudo systemctl enable --now postgresql
```

---

## 2. Connecting to PostgreSQL

### Connect as the local PostgreSQL administrator

```bash
sudo -u postgres psql
```

### Connect to a specific database

```bash
sudo -u postgres psql -d hydra_auctions
```

### Connect with a PostgreSQL username

```bash
psql -U hydra_user -d hydra_auctions
```

### Connect to a remote PostgreSQL server

```bash
psql -h 192.168.1.50 -p 5432 -U hydra_user -d hydra_auctions
```

### Force a password prompt

```bash
psql -h localhost -U hydra_user -d hydra_auctions -W
```

### Connect using a PostgreSQL URI

```bash
psql "postgresql://hydra_user:password@localhost:5432/hydra_auctions"
```

Avoid putting real passwords directly in shell history when possible.

---

## 3. Useful `psql` Commands

These commands are entered from inside the `psql` prompt.

### Show connection information

```text
\conninfo
```

### List databases

```text
\l
```

or:

```text
\list
```

### Connect to another database

```text
\c hydra_auctions
```

### List PostgreSQL users and roles

```text
\du
```

### List schemas

```text
\dn
```

### List tables

```text
\dt
```

### List tables in all schemas

```text
\dt *.*
```

### Describe a table

```text
\d auctions
```

### Show detailed table information

```text
\d+ auctions
```

### List indexes

```text
\di
```

### List sequences

```text
\ds
```

### List views

```text
\dv
```

### Show command history

```text
\s
```

### Run a SQL file

```text
\i /path/to/schema.sql
```

### Toggle expanded output

Useful for wide rows:

```text
\x
```

### Show query execution time

```text
\timing
```

### Quit `psql`

```text
\q
```

---

## 4. Database and User Setup

### Create a PostgreSQL user

Run from `psql` as an administrator:

```sql
CREATE USER hydra_user WITH PASSWORD 'replace-with-secure-password';
```

### Create a login role

```sql
CREATE ROLE hydra_user
WITH LOGIN
PASSWORD 'replace-with-secure-password';
```

### Create a database

```sql
CREATE DATABASE hydra_auctions;
```

### Create a database owned by a specific user

```sql
CREATE DATABASE hydra_auctions
OWNER hydra_user;
```

### Create a database from the shell

```bash
sudo -u postgres createdb hydra_auctions
```

### Create a user from the shell

```bash
sudo -u postgres createuser --pwprompt hydra_user
```

### Change a user's password

```sql
ALTER USER hydra_user
WITH PASSWORD 'new-secure-password';
```

### Change database ownership

```sql
ALTER DATABASE hydra_auctions
OWNER TO hydra_user;
```

### Allow a user to connect to a database

```sql
GRANT CONNECT ON DATABASE hydra_auctions TO hydra_user;
```

### Grant all database privileges

```sql
GRANT ALL PRIVILEGES ON DATABASE hydra_auctions TO hydra_user;
```

Database-level privileges do not automatically grant full access to existing tables.

---

## 5. Schema Setup and Permissions

### Create a schema

```sql
CREATE SCHEMA hydra
AUTHORIZATION hydra_user;
```

### Create a schema only if it does not exist

```sql
CREATE SCHEMA IF NOT EXISTS hydra
AUTHORIZATION hydra_user;
```

### Change schema ownership

```sql
ALTER SCHEMA public
OWNER TO hydra_user;
```

### Grant access to a schema

```sql
GRANT USAGE ON SCHEMA public TO hydra_user;
```

### Allow object creation in a schema

```sql
GRANT CREATE ON SCHEMA public TO hydra_user;
```

### Grant access to all existing tables

```sql
GRANT SELECT, INSERT, UPDATE, DELETE
ON ALL TABLES IN SCHEMA public
TO hydra_user;
```

### Grant access to all existing sequences

Required for `SERIAL` and identity columns:

```sql
GRANT USAGE, SELECT, UPDATE
ON ALL SEQUENCES IN SCHEMA public
TO hydra_user;
```

### Set default privileges for future tables

Run this as the role that will create the tables:

```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT SELECT, INSERT, UPDATE, DELETE
ON TABLES TO hydra_user;
```

### Set default privileges for future sequences

```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT USAGE, SELECT, UPDATE
ON SEQUENCES TO hydra_user;
```

---

## 6. Basic Table Creation

### Create a simple table

```sql
CREATE TABLE users (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    email VARCHAR(255),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### Create a table only if it does not exist

```sql
CREATE TABLE IF NOT EXISTS users (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### Example auction table

```sql
CREATE TABLE auctions (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    scan_id BIGINT NOT NULL,
    item_id INTEGER NOT NULL,
    name TEXT NOT NULL,
    quantity INTEGER NOT NULL CHECK (quantity > 0),
    buyout BIGINT NOT NULL CHECK (buyout >= 0),
    faction VARCHAR(20),
    scanned_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### Create a foreign key

```sql
ALTER TABLE auctions
ADD CONSTRAINT auctions_scan_id_fkey
FOREIGN KEY (scan_id)
REFERENCES scans(id)
ON DELETE CASCADE;
```

### Create an index

```sql
CREATE INDEX idx_auctions_item_id
ON auctions(item_id);
```

### Create a multi-column index

```sql
CREATE INDEX idx_auctions_item_faction
ON auctions(item_id, faction);
```

### Create a unique index

```sql
CREATE UNIQUE INDEX idx_users_email_unique
ON users(email);
```

---

## 7. Common Data Commands

### Insert a row

```sql
INSERT INTO users (username, email)
VALUES ('example_user', 'user@example.com');
```

### Insert multiple rows

```sql
INSERT INTO users (username, email)
VALUES
    ('user1', 'user1@example.com'),
    ('user2', 'user2@example.com');
```

### Query all rows

```sql
SELECT *
FROM users;
```

### Query selected columns

```sql
SELECT id, username, created_at
FROM users;
```

### Filter rows

```sql
SELECT *
FROM auctions
WHERE item_id = 818;
```

### Sort results

```sql
SELECT *
FROM auctions
ORDER BY scanned_at DESC;
```

### Limit results

```sql
SELECT *
FROM auctions
ORDER BY scanned_at DESC
LIMIT 25;
```

### Update rows

```sql
UPDATE users
SET email = 'new-address@example.com'
WHERE username = 'example_user';
```

### Delete rows

```sql
DELETE FROM users
WHERE username = 'example_user';
```

### Count rows

```sql
SELECT COUNT(*)
FROM auctions;
```

### Count rows by value

```sql
SELECT faction, COUNT(*)
FROM auctions
GROUP BY faction
ORDER BY COUNT(*) DESC;
```

---

## 8. Modifying Tables

### Add a column

```sql
ALTER TABLE users
ADD COLUMN last_login TIMESTAMPTZ;
```

### Add a column with a default

```sql
ALTER TABLE users
ADD COLUMN active BOOLEAN NOT NULL DEFAULT TRUE;
```

### Rename a column

```sql
ALTER TABLE users
RENAME COLUMN username TO account_name;
```

### Change a column data type

```sql
ALTER TABLE auctions
ALTER COLUMN buyout TYPE NUMERIC(20, 0);
```

For complex conversions, use `USING`:

```sql
ALTER TABLE users
ALTER COLUMN id TYPE BIGINT
USING id::BIGINT;
```

### Set a default value

```sql
ALTER TABLE users
ALTER COLUMN created_at SET DEFAULT NOW();
```

### Remove a default value

```sql
ALTER TABLE users
ALTER COLUMN created_at DROP DEFAULT;
```

### Add a `NOT NULL` constraint

```sql
ALTER TABLE users
ALTER COLUMN email SET NOT NULL;
```

Before doing this, verify that no existing rows contain `NULL`.

### Remove a `NOT NULL` constraint

```sql
ALTER TABLE users
ALTER COLUMN email DROP NOT NULL;
```

### Add a check constraint

```sql
ALTER TABLE auctions
ADD CONSTRAINT auctions_buyout_nonnegative
CHECK (buyout >= 0);
```

### Drop a constraint

```sql
ALTER TABLE auctions
DROP CONSTRAINT auctions_buyout_nonnegative;
```

### Rename a table

```sql
ALTER TABLE users
RENAME TO application_users;
```

### Change table ownership

```sql
ALTER TABLE auctions
OWNER TO hydra_user;
```

### Drop a column

```sql
ALTER TABLE users
DROP COLUMN last_login;
```

### Drop a table

```sql
DROP TABLE users;
```

### Drop a table only if it exists

```sql
DROP TABLE IF EXISTS users;
```

### Drop a table and dependent objects

Use carefully:

```sql
DROP TABLE IF EXISTS users CASCADE;
```

---

## 9. Transactions

Transactions allow multiple changes to succeed or fail together.

### Start a transaction

```sql
BEGIN;
```

### Commit changes

```sql
COMMIT;
```

### Undo uncommitted changes

```sql
ROLLBACK;
```

### Example safe update

```sql
BEGIN;

UPDATE auctions
SET buyout = 0
WHERE item_id = 818;

SELECT *
FROM auctions
WHERE item_id = 818;

COMMIT;
```

Replace `COMMIT` with `ROLLBACK` if the results are incorrect.

---

## 10. Inspecting Database Objects

### Show table columns using SQL

```sql
SELECT
    column_name,
    data_type,
    is_nullable,
    column_default
FROM information_schema.columns
WHERE table_schema = 'public'
  AND table_name = 'auctions'
ORDER BY ordinal_position;
```

### Show table constraints

```sql
SELECT
    constraint_name,
    constraint_type
FROM information_schema.table_constraints
WHERE table_schema = 'public'
  AND table_name = 'auctions';
```

### Show indexes for a table

```sql
SELECT
    indexname,
    indexdef
FROM pg_indexes
WHERE schemaname = 'public'
  AND tablename = 'auctions';
```

### Show database sizes

```sql
SELECT
    datname,
    pg_size_pretty(pg_database_size(datname)) AS size
FROM pg_database
ORDER BY pg_database_size(datname) DESC;
```

### Show table sizes

```sql
SELECT
    schemaname,
    relname,
    pg_size_pretty(pg_total_relation_size(relid)) AS total_size
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;
```

### Show active connections

```sql
SELECT
    pid,
    usename,
    datname,
    client_addr,
    state,
    query_start,
    query
FROM pg_stat_activity
ORDER BY query_start;
```

---

# Database Backups

## 11. Dump the Full Database

A full database dump includes schema definitions and data.

### Custom-format backup

Recommended for most backups:

```bash
pg_dump \
  -h localhost \
  -p 5432 \
  -U hydra_user \
  -d hydra_auctions \
  -F c \
  -f hydra_auctions.dump
```

Options:

- `-F c`: custom archive format
- `-f`: output file
- `-d`: database name
- `-U`: PostgreSQL username
- `-h`: PostgreSQL host
- `-p`: PostgreSQL port

### Plain SQL full database backup

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  -F p \
  -f hydra_auctions.sql
```

A plain SQL backup can be viewed in a text editor and restored with `psql`.

### Compressed plain SQL backup

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  | gzip > hydra_auctions.sql.gz
```

### Timestamped backup filename

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  -F c \
  -f "hydra_auctions_$(date +%F_%H-%M-%S).dump"
```

---

## 12. Dump Schema Only

A schema-only dump contains tables, sequences, constraints, indexes, functions, and other definitions, but no table data.

### Schema-only custom-format dump

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --schema-only \
  -F c \
  -f hydra_auctions_schema.dump
```

### Schema-only plain SQL dump

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --schema-only \
  -f hydra_auctions_schema.sql
```

### Dump one PostgreSQL schema only

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --schema=public \
  --schema-only \
  -f public_schema.sql
```

### Dump schema without ownership statements

Useful when restoring under a different PostgreSQL user:

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --schema-only \
  --no-owner \
  --no-privileges \
  -f hydra_auctions_schema.sql
```

---

## 13. Dump Data Only

### Dump all table data without schema definitions

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --data-only \
  -f hydra_auctions_data.sql
```

### Dump data using custom format

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --data-only \
  -F c \
  -f hydra_auctions_data.dump
```

---

## 14. Dump Specific Tables

### Dump one table with schema and data

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --table=auctions \
  -f auctions.sql
```

### Dump one table's data only

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --table=auctions \
  --data-only \
  -f auctions_data.sql
```

### Dump multiple tables

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --table=scans \
  --table=auctions \
  -F c \
  -f auction_tables.dump
```

### Exclude a table

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --exclude-table=temporary_data \
  -F c \
  -f hydra_without_temp.dump
```

### Exclude table data but keep its schema

```bash
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --exclude-table-data=logs \
  -F c \
  -f hydra_without_log_data.dump
```

---

## 15. Dump All PostgreSQL Databases

`pg_dumpall` creates a plain SQL file containing all databases and global objects.

```bash
sudo -u postgres pg_dumpall > all_databases.sql
```

Compressed version:

```bash
sudo -u postgres pg_dumpall | gzip > all_databases.sql.gz
```

### Dump only PostgreSQL roles and tablespaces

```bash
sudo -u postgres pg_dumpall --globals-only > postgres_globals.sql
```

This is useful because normal `pg_dump` backups do not include cluster-wide roles.

---

# Database Restores

## 16. Restore a Plain SQL Backup

Create the target database first:

```bash
sudo -u postgres createdb hydra_auctions
```

Restore the SQL file:

```bash
psql \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  -f hydra_auctions.sql
```

### Restore a compressed SQL backup

```bash
gunzip -c hydra_auctions.sql.gz \
  | psql -h localhost -U hydra_user -d hydra_auctions
```

---

## 17. Restore a Custom-Format Backup

### Restore into an existing database

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  hydra_auctions.dump
```

### Drop existing objects before restoring

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --clean \
  --if-exists \
  hydra_auctions.dump
```

### Restore without original ownership

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --no-owner \
  hydra_auctions.dump
```

### Restore and create the database from the dump

The dump must contain database creation metadata:

```bash
pg_restore \
  -h localhost \
  -U postgres \
  --create \
  -d postgres \
  hydra_auctions.dump
```

### Restore only the schema

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --schema-only \
  hydra_auctions.dump
```

### Restore only the data

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --data-only \
  hydra_auctions.dump
```

### List backup contents

```bash
pg_restore --list hydra_auctions.dump
```

### Restore in parallel

Custom and directory formats support parallel restore:

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --jobs=4 \
  hydra_auctions.dump
```

---

## 18. Restore All Databases and Roles

```bash
sudo -u postgres psql -f all_databases.sql
```

For a compressed dump:

```bash
gunzip -c all_databases.sql.gz \
  | sudo -u postgres psql
```

---

## 19. Recreate a Database Before Restore

Terminate existing connections:

```sql
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE datname = 'hydra_auctions'
  AND pid <> pg_backend_pid();
```

Drop and recreate the database:

```bash
sudo -u postgres dropdb hydra_auctions
sudo -u postgres createdb -O hydra_user hydra_auctions
```

Restore:

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --no-owner \
  hydra_auctions.dump
```

---

# Docker PostgreSQL

## 20. Basic Docker Compose PostgreSQL Service

```yaml
services:
  db:
    image: postgres:16
    container_name: hydra-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - hydra_postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  hydra_postgres_data:
```

Example `.env` file:

```dotenv
POSTGRES_DB=hydra_auctions
POSTGRES_USER=hydra_user
POSTGRES_PASSWORD=replace-with-secure-password
```

The final `volumes:` section declares the named volume. Docker creates and manages the actual storage location.

---

## 21. Start and Inspect the Container

```bash
docker compose up -d
```

```bash
docker compose ps
```

```bash
docker compose logs db
```

```bash
docker compose logs -f db
```

```bash
docker inspect hydra-postgres
```

---

## 22. Connect to PostgreSQL Inside Docker

```bash
docker exec -it hydra-postgres \
  psql -U hydra_user -d hydra_auctions
```

Using Docker Compose:

```bash
docker compose exec db \
  psql -U hydra_user -d hydra_auctions
```

---

## 23. Back Up a Docker PostgreSQL Database

### Custom-format backup to the host

```bash
docker exec hydra-postgres \
  pg_dump -U hydra_user -d hydra_auctions -F c \
  > hydra_auctions.dump
```

### Plain SQL backup to the host

```bash
docker exec hydra-postgres \
  pg_dump -U hydra_user -d hydra_auctions \
  > hydra_auctions.sql
```

### Schema-only Docker backup

```bash
docker exec hydra-postgres \
  pg_dump -U hydra_user -d hydra_auctions --schema-only \
  > hydra_auctions_schema.sql
```

### Data-only Docker backup

```bash
docker exec hydra-postgres \
  pg_dump -U hydra_user -d hydra_auctions --data-only \
  > hydra_auctions_data.sql
```

### Timestamped Docker backup

```bash
docker exec hydra-postgres \
  pg_dump -U hydra_user -d hydra_auctions -F c \
  > "hydra_auctions_$(date +%F_%H-%M-%S).dump"
```

---

## 24. Restore a Docker PostgreSQL Database

### Restore a plain SQL file

```bash
cat hydra_auctions.sql \
  | docker exec -i hydra-postgres \
      psql -U hydra_user -d hydra_auctions
```

### Restore a custom-format dump

Copy the dump into the container:

```bash
docker cp hydra_auctions.dump \
  hydra-postgres:/tmp/hydra_auctions.dump
```

Restore it:

```bash
docker exec -it hydra-postgres \
  pg_restore \
  -U hydra_user \
  -d hydra_auctions \
  --clean \
  --if-exists \
  --no-owner \
  /tmp/hydra_auctions.dump
```

Remove the copied dump:

```bash
docker exec hydra-postgres \
  rm /tmp/hydra_auctions.dump
```

### Restore through standard input

```bash
docker exec -i hydra-postgres \
  pg_restore \
  -U hydra_user \
  -d hydra_auctions \
  --no-owner \
  < hydra_auctions.dump
```

---

## 25. PostgreSQL Initialization Scripts in Docker

SQL and shell files placed in `/docker-entrypoint-initdb.d/` run only when the PostgreSQL data directory is first initialized.

Example Compose mount:

```yaml
services:
  db:
    image: postgres:16
    volumes:
      - hydra_postgres_data:/var/lib/postgresql/data
      - ./database/init:/docker-entrypoint-initdb.d:ro
```

Example project structure:

```text
database/
└── init/
    ├── 01-schema.sql
    └── 02-seed-data.sql
```

Important:

- Initialization scripts do not run again when an existing named volume is reused.
- Removing the volume deletes the database data.
- Use migrations such as Alembic for ongoing schema changes.

Remove containers and the database volume:

```bash
docker compose down -v
```

This permanently deletes the database stored in the Compose volume.

---

# PostgreSQL Authentication and Configuration

## 26. Find PostgreSQL Configuration Files

From `psql`:

```sql
SHOW config_file;
```

```sql
SHOW hba_file;
```

```sql
SHOW data_directory;
```

Common files:

```text
postgresql.conf
pg_hba.conf
pg_ident.conf
```

---

## 27. Allow Password Authentication

Example `pg_hba.conf` entries:

```text
host    all    all    127.0.0.1/32    scram-sha-256
host    all    all    ::1/128         scram-sha-256
```

For a trusted private network:

```text
host    hydra_auctions    hydra_user    192.168.1.0/24    scram-sha-256
```

Reload PostgreSQL after changing `pg_hba.conf`:

```bash
sudo systemctl reload postgresql
```

or from SQL:

```sql
SELECT pg_reload_conf();
```

---

## 28. Listen on Network Interfaces

In `postgresql.conf`:

```text
listen_addresses = '*'
port = 5432
```

Restart PostgreSQL:

```bash
sudo systemctl restart postgresql
```

Open the firewall only for required sources:

```bash
sudo firewall-cmd \
  --permanent \
  --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port protocol="tcp" port="5432" accept'
```

```bash
sudo firewall-cmd --reload
```

Do not expose PostgreSQL directly to the public internet unless it is protected by strong network controls.

---

# Password Handling

## 29. Use a `.pgpass` File

Create:

```bash
nano ~/.pgpass
```

Format:

```text
hostname:port:database:username:password
```

Example:

```text
localhost:5432:hydra_auctions:hydra_user:replace-with-password
```

Set required permissions:

```bash
chmod 600 ~/.pgpass
```

Then run commands without an interactive password prompt:

```bash
pg_dump -h localhost -U hydra_user -d hydra_auctions -F c -f backup.dump
```

---

## 30. Use `PGPASSWORD` Temporarily

```bash
PGPASSWORD='replace-with-password' \
pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  -F c \
  -f hydra_auctions.dump
```

This can expose the password to process inspection or shell history. Prefer `.pgpass`, Docker secrets, or another secrets manager.

---

# Backup Automation

## 31. Simple Backup Script

```bash
#!/usr/bin/env bash

set -euo pipefail

BACKUP_DIR="/var/backups/postgresql"
DATABASE="hydra_auctions"
USER_NAME="hydra_user"
HOST_NAME="localhost"
RETENTION_DAYS=14
TIMESTAMP="$(date +%F_%H-%M-%S)"
BACKUP_FILE="${BACKUP_DIR}/${DATABASE}_${TIMESTAMP}.dump"

mkdir -p "$BACKUP_DIR"

pg_dump \
  -h "$HOST_NAME" \
  -U "$USER_NAME" \
  -d "$DATABASE" \
  -F c \
  -f "$BACKUP_FILE"

find "$BACKUP_DIR" \
  -type f \
  -name "${DATABASE}_*.dump" \
  -mtime +"$RETENTION_DAYS" \
  -delete

echo "Backup completed: $BACKUP_FILE"
```

Make it executable:

```bash
chmod +x /usr/local/sbin/backup-hydra-postgres.sh
```

Run it:

```bash
/usr/local/sbin/backup-hydra-postgres.sh
```

---

## 32. Cron Backup Example

Edit the current user's crontab:

```bash
crontab -e
```

Run every day at 2:00 AM:

```cron
0 2 * * * /usr/local/sbin/backup-hydra-postgres.sh >> /var/log/hydra-postgres-backup.log 2>&1
```

---

## 33. systemd Service and Timer

Service file:

```ini
# /etc/systemd/system/hydra-postgres-backup.service

[Unit]
Description=Back up Hydra PostgreSQL database

[Service]
Type=oneshot
ExecStart=/usr/local/sbin/backup-hydra-postgres.sh
```

Timer file:

```ini
# /etc/systemd/system/hydra-postgres-backup.timer

[Unit]
Description=Run Hydra PostgreSQL backup daily

[Timer]
OnCalendar=*-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

Enable the timer:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now hydra-postgres-backup.timer
```

Check the timer:

```bash
systemctl list-timers hydra-postgres-backup.timer
```

Run the backup manually:

```bash
sudo systemctl start hydra-postgres-backup.service
```

View logs:

```bash
journalctl -u hydra-postgres-backup.service
```

---

# Maintenance and Troubleshooting

## 34. Analyze and Vacuum

Update query planner statistics:

```sql
ANALYZE;
```

Vacuum all tables in the current database:

```sql
VACUUM;
```

Vacuum and analyze:

```sql
VACUUM ANALYZE;
```

Reclaim more disk space with an exclusive lock:

```sql
VACUUM FULL;
```

Use `VACUUM FULL` carefully because it locks and rewrites tables.

### Run from the shell

```bash
vacuumdb \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  --analyze
```

---

## 35. Reindex

Reindex one table:

```sql
REINDEX TABLE auctions;
```

Reindex a database:

```sql
REINDEX DATABASE hydra_auctions;
```

---

## 36. Check Long-Running Queries

```sql
SELECT
    pid,
    now() - query_start AS duration,
    usename,
    datname,
    state,
    query
FROM pg_stat_activity
WHERE state <> 'idle'
ORDER BY duration DESC;
```

Cancel a query:

```sql
SELECT pg_cancel_backend(12345);
```

Terminate a connection:

```sql
SELECT pg_terminate_backend(12345);
```

---

## 37. Check Locks

```sql
SELECT
    locktype,
    database,
    relation::regclass,
    mode,
    granted,
    pid
FROM pg_locks
ORDER BY granted, pid;
```

---

## 38. Common Error: Peer Authentication Failed

Example error:

```text
FATAL: Peer authentication failed for user "postgres"
```

Use the operating-system `postgres` account:

```bash
sudo -u postgres psql
```

Or connect using TCP and password authentication:

```bash
psql -h localhost -U postgres -d postgres -W
```

The authentication behavior is controlled by `pg_hba.conf`.

---

## 39. Common Error: Server and Client Version Mismatch

Example:

```text
pg_dump: error: server version: 16.x
pg_dump version: 13.x
```

Use a `pg_dump` version that is the same major version as the server or newer.

Check versions:

```bash
pg_dump --version
```

```sql
SELECT version();
```

On RHEL-based systems, install the matching PostgreSQL client package or use the `pg_dump` binary from the matching PostgreSQL installation.

Example path:

```bash
/usr/pgsql-16/bin/pg_dump --version
```

Example backup:

```bash
/usr/pgsql-16/bin/pg_dump \
  -h localhost \
  -U hydra_user \
  -d hydra_auctions \
  -F c \
  -f hydra_auctions.dump
```

---

## 40. Common Error: Could Not Change Directory

Example:

```text
could not change directory to "/root": Permission denied
```

This commonly happens when using:

```bash
sudo -u postgres ...
```

from a directory the `postgres` operating-system user cannot access.

Change to a readable directory first:

```bash
cd /tmp
sudo -u postgres pg_dump hydra_auctions > /tmp/hydra_auctions.sql
```

Or write the backup to a directory owned by `postgres`.

---

# Roles and Security

## 41. Role Management

### Create a read-only role

```sql
CREATE ROLE hydra_readonly;
```

```sql
GRANT CONNECT ON DATABASE hydra_auctions TO hydra_readonly;
```

```sql
GRANT USAGE ON SCHEMA public TO hydra_readonly;
```

```sql
GRANT SELECT ON ALL TABLES IN SCHEMA public TO hydra_readonly;
```

```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT SELECT ON TABLES TO hydra_readonly;
```

### Assign the role to a user

```sql
GRANT hydra_readonly TO reporting_user;
```

### Remove a role from a user

```sql
REVOKE hydra_readonly FROM reporting_user;
```

### Remove login access

```sql
ALTER ROLE hydra_user NOLOGIN;
```

### Restore login access

```sql
ALTER ROLE hydra_user LOGIN;
```

### Drop a user or role

```sql
DROP ROLE hydra_user;
```

Before dropping a role, ownership and privileges may need to be reassigned:

```sql
REASSIGN OWNED BY hydra_user TO postgres;
DROP OWNED BY hydra_user;
DROP ROLE hydra_user;
```

---

# Migration Workflow

## 42. Recommended Schema Change Workflow

1. Back up the database.
2. Test the change against a development or staging database.
3. Put changes in a migration file.
4. Run the migration inside a transaction when possible.
5. Verify the schema and application behavior.
6. Keep the migration file in source control.

Example SQL migration:

```sql
BEGIN;

ALTER TABLE auctions
ADD COLUMN source VARCHAR(50);

CREATE INDEX idx_auctions_source
ON auctions(source);

COMMIT;
```

Rollback migration:

```sql
BEGIN;

DROP INDEX IF EXISTS idx_auctions_source;

ALTER TABLE auctions
DROP COLUMN IF EXISTS source;

COMMIT;
```

For Python applications using SQLAlchemy, Alembic is commonly used to track and apply schema migrations.

---

# Quick Reference

## 43. Frequently Used Commands

### Connect locally

```bash
sudo -u postgres psql
```

### List databases

```text
\l
```

### List users

```text
\du
```

### List tables

```text
\dt
```

### Describe a table

```text
\d+ auctions
```

### Full database dump

```bash
pg_dump -h localhost -U hydra_user -d hydra_auctions -F c -f hydra_auctions.dump
```

### Schema-only dump

```bash
pg_dump -h localhost -U hydra_user -d hydra_auctions --schema-only -f hydra_schema.sql
```

### Data-only dump

```bash
pg_dump -h localhost -U hydra_user -d hydra_auctions --data-only -f hydra_data.sql
```

### Restore custom dump

```bash
pg_restore -h localhost -U hydra_user -d hydra_auctions --no-owner hydra_auctions.dump
```

### Restore plain SQL

```bash
psql -h localhost -U hydra_user -d hydra_auctions -f hydra_auctions.sql
```

### Docker full database dump

```bash
docker exec hydra-postgres pg_dump -U hydra_user -d hydra_auctions -F c > hydra_auctions.dump
```

### Docker schema dump

```bash
docker exec hydra-postgres pg_dump -U hydra_user -d hydra_auctions --schema-only > hydra_schema.sql
```

### Docker restore

```bash
docker exec -i hydra-postgres pg_restore -U hydra_user -d hydra_auctions --no-owner < hydra_auctions.dump
```

---

# Backup Checklist

Before relying on a backup:

- Confirm the backup command completed successfully.
- Confirm the backup file is not empty.
- Store copies outside the PostgreSQL server or Docker host.
- Encrypt backups containing sensitive information.
- Test restores regularly.
- Keep PostgreSQL role backups when needed.
- Keep multiple generations of backups.
- Document the PostgreSQL major version used to create each backup.

Check file size:

```bash
ls -lh hydra_auctions.dump
```

Inspect a custom backup:

```bash
pg_restore --list hydra_auctions.dump | head
```

Test a restore into a temporary database:

```bash
createdb -h localhost -U hydra_user hydra_restore_test
```

```bash
pg_restore \
  -h localhost \
  -U hydra_user \
  -d hydra_restore_test \
  --no-owner \
  hydra_auctions.dump
```

Delete the test database:

```bash
dropdb -h localhost -U hydra_user hydra_restore_test
```
