# What is Operating System Hardening?
Operating system hardening is the process of securing a computer's operating system to reduce its vulnerability to cyber threats and attacks. In simple terms, it's like putting a strong armor around your computer to protect it from potential dangers.

## Why is it Important?
Imagine your operating system (like Windows, macOS, or Linux) as the front door of your house. If it's not properly secured, anyone with malicious intentions can break in. Hardening your operating system is like adding locks, alarms, and reinforced doors to make sure only authorized individuals can access your system.

## Key Concepts in Operating System Hardening:
- Updates and Patches: Regularly update your operating system and software. Think of these updates as fixes or improvements to your house's security system. They often include patches that close security holes.
- User Accounts and Permissions: Be mindful of who has access to your system. Create strong passwords for your user accounts and limit access to only those who need it. This is like having a guest list for a party - only invite the people you trust.
- Firewalls: Firewalls act like guards at the entrance of your digital space. They monitor and control incoming and outgoing network traffic. Configure your firewall settings to only allow necessary and safe connections.
- Antivirus and Anti-malware: Install antivirus software to scan and remove malicious programs. Think of it as having a vigilant security guard looking out for anything suspicious.
- Encryption: Encrypt sensitive data to make it unreadable to unauthorized individuals. It's like turning your important documents into a secret code that only you can understand.
- Disable Unnecessary Services: Turn off any services or features that you don't need. It's like closing unnecessary doors in your house – the fewer entry points, the harder it is for someone to break in.
- Backups: Regularly back up your important files. This is like creating duplicate keys for your house. If something goes wrong, you can always restore your system to a previous, safer state.


Operating system hardening is essentially about making your digital environment more secure. By following these basic steps, you're building a strong defense against potential cyber threats. Just like in the physical world, a well-protected home is less likely to be targeted by intruders.

---

## Hardening checklists a pentester validates

Hardening is measured against benchmarks. During a pentest you verify these controls are actually in place — every gap is a finding.

### Linux hardening essentials

```bash
# Disable root SSH login and enforce key auth
# /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no

# Remove SUID from unnecessary binaries; audit with:
find / -perm -4000 -type f 2>/dev/null

# Enable and configure the firewall
ufw default deny incoming && ufw enable

# Audit with automated tools
lynis audit system
```

### Windows hardening essentials

- Enforce **strong password / lockout policy** (`secpol.msc`).
- Enable **BitLocker** disk encryption and **Credential Guard**.
- Disable **SMBv1**, LLMNR, and NBT-NS (kills poisoning attacks).
- Restrict local admin; use **LAPS** for local admin password rotation.
- Enable **Attack Surface Reduction** rules and application allowlisting.

### Verify with benchmarks

```bash
# Map the host against CIS controls
lynis audit system                    # Linux/macOS
# CIS-CAT Pro / Microsoft Security Compliance Toolkit  (Windows)
```

### The hardening pyramid

1. **Patch** — eliminate known CVEs (biggest ROI).
2. **Reduce attack surface** — disable unused services/ports/protocols.
3. **Least privilege** — accounts, services, and file permissions.
4. **Defense in depth** — firewall, EDR, encryption, logging.
5. **Monitor** — logging + alerting so exploitation is detected.

## Related

- [CIS Benchmark](../Cloud/CIS%20Benchmark.md)
- [Linux](Linux.md) · [Windows](Windows.md)
