# Injection (A03:2021)

Injection happens when untrusted input is sent to an interpreter (a database, shell, LDAP directory, browser, etc.) as part of a command or query, so the input **changes the meaning** of that command. The interpreter can no longer tell your data from its own code. In 2021 OWASP merged **XSS** into this category.

The core mistake, everywhere, is the same:

```python
# VULNERABLE - data is concatenated into code
query = "SELECT * FROM users WHERE name = '" + name + "'"
```

## The injection family (each has a dedicated guide)

- **SQL Injection** -> a database - see [SQL Injection](../Vulnerabilities/SQL%20Injection.md)
- **Cross-Site Scripting (XSS)** -> the browser - see [XSS](../Vulnerabilities/XSS.md)
- **OS Command Injection** -> a shell - see [RCE](../Vulnerabilities/RCE.md)
- **LDAP Injection** -> a directory - see [LDAP Injection](../Vulnerabilities/LDAP%20Injection.md)
- **NoSQL Injection** -> MongoDB/others (below)
- **SSTI, XPath, XXE, Header/CRLF injection** - same root cause, different interpreter

## Quick detection payloads by type

```text
SQL:      '   "   ' OR '1'='1' --   ' AND SLEEP(5)--
NoSQL:    {"$ne": null}   {"$gt": ""}   ' || '1'=='1
Command:  ; id    | id    `id`    $(id)    %0a id
LDAP:     *   *)(uid=*   admin)(&)
XSS:      <script>alert(1)</script>   "><img src=x onerror=alert(1)>
XPath:    ' or '1'='1
```

## NoSQL injection example (MongoDB)

A login checking `db.users.find({user: username, pass: password})` can be bypassed:

```json
// Send as JSON body
{"user": {"$ne": null}, "pass": {"$ne": null}}   // matches the first user
{"user": "admin", "pass": {"$gt": ""}}            // log in as admin
```

```text
# Or in URL-encoded form parameters
user[$ne]=x&pass[$ne]=x
```

## General testing methodology

1. Enumerate **every** input: query/body params, headers, cookies, JSON fields, file names.
2. Inject the metacharacters for the interpreter you suspect (see table).
3. Watch for errors, changed responses, delays, or callbacks (blind).
4. Confirm, then escalate to data extraction / command execution / script execution.
5. Automate with the right tool (sqlmap, dalfox, commix, NoSQLMap).

## Tools

- [sqlmap](https://sqlmap.org/) (SQL), [NoSQLMap](https://github.com/codingo/NoSQLMap) (NoSQL)
- [commix](https://github.com/commixproject/commix) (command), [dalfox](https://github.com/hahwul/dalfox) (XSS)
- [Burp Suite](https://portswigger.net/burp) - the hub for manual injection testing

## Mitigation - the fix

- **Parameterized queries / prepared statements** for all database access.
- **Context-aware output encoding** for XSS; use framework auto-escaping.
- Use **safe APIs** that take argument arrays instead of building shell strings.
- Validate/allowlist input; escape special characters per interpreter.
- Least privilege for DB/OS accounts; add a WAF as defense in depth (not the fix).

## Practice

- [PortSwigger SQLi](https://portswigger.net/web-security/sql-injection) and [XSS](https://portswigger.net/web-security/cross-site-scripting) labs

## Reference

- [OWASP A03:2021 Injection](https://owasp.org/Top10/A03_2021-Injection/)
