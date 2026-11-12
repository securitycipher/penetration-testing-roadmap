# DCSync

DCSync is a technique that abuses the normal domain-replication protocol (MS-DRSR) to ask a Domain Controller for the password data of any account - including `krbtgt` and Domain Admins. Instead of touching LSASS on the DC, you pretend to be another DC requesting a sync. Any principal with replication rights can do it from anywhere in the domain.

## Why it matters

DCSync gives you the NT hashes for every account you ask for without running code on the DC. Pulling the **krbtgt** hash enables [Golden Ticket](Kerberoasting.md) persistence. It is the standard "game over" step once you have enough rights.

## Who can run it

You need one of these replication rights on the domain object:

- `DS-Replication-Get-Changes`
- `DS-Replication-Get-Changes-All`

Domain Admins have them by default. A frequent finding is a non-admin user or group that was accidentally granted these rights - [BloodHound](BloodHound.md) flags this as a `DCSync` edge.

## Running it

```bash
# Impacket - dump a single account
impacket-secretsdump -just-dc-user krbtgt domain/user:'Password1'@10.0.0.1

# Dump the whole domain
impacket-secretsdump domain/user:'Password1'@10.0.0.1
```

```text
:: Mimikatz (on a Windows foothold)
lsadump::dcsync /domain:corp.local /user:krbtgt
lsadump::dcsync /domain:corp.local /user:Administrator
```

## After DCSync

- Use the NT hashes with [Pass-the-Hash](Pass-the-Hash.md)
- Forge a Golden Ticket with the `krbtgt` hash for long-term persistence
- Crack weak hashes offline with [Hashcat](../Tools/Hashcat.md)

## Mitigation

- Audit who holds replication rights; remove unexpected grants (check [BloodHound](BloodHound.md)).
- Rotate the **krbtgt** password twice (carefully) if compromise is suspected.
- Monitor for replication (DRSUAPI) requests from non-DC hosts.
- Tier admin accounts so one compromised workstation cannot reach these rights.

## Related

- [Pass-the-Hash](Pass-the-Hash.md), [Kerberoasting](Kerberoasting.md)
- [BloodHound](BloodHound.md), [Impacket](../Tools/Impacket.md)
