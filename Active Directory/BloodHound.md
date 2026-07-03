# BloodHound

BloodHound maps Active Directory relationships to find the shortest path from your current user to Domain Admin.

## What it shows

- Users, groups, computers, GPOs, and ACLs as a graph
- **Shortest paths** to high-value targets
- Misconfigurations: excessive group membership, unconstrained delegation, Kerberoastable users on path to DA

## Setup

1. Deploy BloodHound CE (or legacy) on your attack machine
2. Run SharpHound or BloodHound.py on a domain-joined or credentialed host
3. Import ZIP into BloodHound UI
4. Run pre-built queries: "Shortest Paths to Domain Admins", "Kerberoastable Users"

```bash
# BloodHound.py ingest
bloodhound-python -d domain.local -u user -p password -ns 10.10.10.10 -c All
```

## Key queries to run first

- Find all Domain Admins
- Shortest path from owned users to Domain Admins
- AS-REP Roastable users
- Computers with unconstrained delegation

## Resources

- [BloodHound docs](https://bloodhound.readthedocs.io/)
- [SpecterOps - BloodHound 101](https://posts.specterops.io/)

## Related

- [Active Directory Basics](Active%20Directory%20Basics.md)
