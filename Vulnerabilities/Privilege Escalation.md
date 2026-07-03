# What is Privilege Escalation?

Privilege Escalation is the step where an attacker turns limited access into higher access. **Vertical** escalation means moving from a normal user to admin/root. **Horizontal** escalation means accessing another user of the same level. On a compromised host it usually means going from a low-privilege shell to `root` or `SYSTEM`.

## Two flavors

- **Vertical** - low-priv account to admin (misconfigured sudo, SUID binaries, kernel bugs)
- **Horizontal** - user A accessing user B's data (often an IDOR under the hood)

## Enumeration commands

```bash
# Linux - quick wins
id; sudo -l                       # what can we run as root?
find / -perm -4000 -type f 2>/dev/null   # SUID binaries
cat /etc/crontab; ls -la /etc/cron.*     # writable cron jobs
uname -a                          # kernel version for known exploits
getcap -r / 2>/dev/null           # dangerous capabilities
```

```powershell
# Windows - quick wins
whoami /priv                      # SeImpersonate? SeBackup?
systeminfo                        # missing patches
Get-Service | ? {$_.Status -eq "Running"}   # unquoted service paths
```

## Tools

- [linPEAS / winPEAS](https://github.com/peass-ng/PEASS-ng) - automated privesc enumeration
- [GTFOBins](https://gtfobins.github.io/) - abuse sudo/SUID binaries
- [LOLBAS](https://lolbas-project.github.io/) - living-off-the-land Windows binaries
- [pspy](https://github.com/DominicBreuker/pspy) - watch cron/processes without root

## Manual testing

1. Run `sudo -l` and check GTFOBins for anything you can run
2. Look for SUID binaries, writable scripts owned by root, and cron jobs
3. Search for credentials in config files, history, and env variables
4. On Windows, check token privileges and unquoted service paths
5. Match the kernel/OS build against public exploits as a last resort

## Mitigation

- Apply least privilege; remove unnecessary sudo rules and SUID bits
- Patch the OS and kernel promptly
- Avoid storing secrets in world-readable files or history
- Monitor for privilege changes and unusual `sudo`/service activity

## Deep dive

- [Privilege Escalation - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [HackTricks privilege escalation](https://book.hacktricks.xyz/)

## CWE

- CWE-269: Improper Privilege Management
- CWE-250: Execution with Unnecessary Privileges
