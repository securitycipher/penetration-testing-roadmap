# Active Directory Basics

Most enterprise internal pentests hit Active Directory (AD). If you plan to do corporate work, AD is not optional.

## Core concepts

- **Domain Controller (DC)** - authenticates users, holds the crown jewels (NTDS.dit)
- **Kerberos** - default auth protocol; tickets (TGT, TGS) can be abused
- **LDAP** - directory queries for users, groups, computers
- **Trust relationships** - child domains, forest trusts expand attack paths

## Common attack paths

1. **Initial access** - phishing, password spray, or exploit on edge system
2. **Enumeration** - BloodHound maps paths to Domain Admin
3. **Credential theft** - Kerberoasting, AS-REP roasting, LLMNR poisoning
4. **Lateral movement** - Pass-the-Hash, WMI, PsExec, RDP
5. **Domain dominance** - DCSync, Golden Ticket

## Essential tools

| Tool | Use |
|------|-----|
| [BloodHound](https://github.com/BloodHoundAD/BloodHound) | Visualize AD attack paths |
| [Impacket](https://github.com/fortra/impacket) | Python AD attack toolkit |
| [Rubeus](https://github.com/GhostPack/Rubeus) | Kerberos abuse |
| [CrackMapExec](https://github.com/Porchetta-Industries/CrackMapExec) | SMB/WinRM spraying |
| [PowerView](https://github.com/PowerShellMafia/PowerSploit) | AD enumeration |

## Practice labs

- [TryHackMe - Attacktive Directory](https://tryhackme.com/room/attacktivedirectory)
- [HackTheBox - Active Directory paths](https://www.hackthebox.com)
- [CRTP course labs](https://www.pentesteracademy.com/red-team-labs)

## Certs

- [CRTP](Certifications/CRTP.md) - recommended after OSCP for AD focus
