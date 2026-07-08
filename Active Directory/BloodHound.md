# BloodHound

BloodHound maps Active Directory relationships to find the shortest path from your current user to Domain Admin.

## What it shows

- Users, groups, computers, GPOs, and ACLs as a graph
- **Shortest paths** to high-value targets
- Misconfigurations: excessive group membership, unconstrained delegation, Kerberoastable users on path to DA

## How it works

BloodHound has two parts: a **collector** that gathers AD data, and the **GUI** (graph DB) that visualizes it. You feed it who-can-do-what data and it computes attack paths a human would never spot manually.

## Step 1 - Collect the data

```bash
# From Linux, with domain creds (no domain-joined host needed)
bloodhound-python -d domain.local -u user -p 'Password1' -ns 10.10.10.10 -c All

# From Windows (SharpHound)
SharpHound.exe -c All --zipfilename loot
# or PowerShell:  Import-Module .\SharpHound.ps1; Invoke-BloodHound -CollectionMethod All
```

Collection methods: `All`, `DCOnly` (stealthy, LDAP only), `Session`, `ACL`, `LoggedOn`.

## Step 2 - Load and analyze

```text
1. Start BloodHound CE (docker compose) or legacy + neo4j.
2. Drag the collected .zip into the UI.
3. Mark your compromised principals as "Owned".
4. Run built-in queries or the Pathfinding search.
```

## Key built-in queries to run first

- **Shortest Paths to Domain Admins**
- **Shortest Paths from Owned Principals**
- **Find Kerberoastable / AS-REP Roastable Users**
- **Computers with Unconstrained Delegation**
- **Find Principals with DCSync Rights**

## Understanding edges (the attack primitives)

```text
MemberOf            - group membership (inherits rights)
AdminTo             - local admin on a computer -> lateral movement
HasSession          - a user is logged in there -> steal their creds
GenericAll/Write    - full control over an object -> reset pw / add to group
ForceChangePassword - reset a user's password
DCSync              - replicate = dump all hashes
AddMember           - add yourself to a privileged group
```

Right-click any edge in the GUI - it explains **exactly** how to abuse it with commands.

## Custom Cypher queries

```cypher
// All users with a path to Domain Admins
MATCH p=shortestPath((u:User)-[*1..]->(g:Group {name:"DOMAIN ADMINS@DOMAIN.LOCAL"}))
RETURN p

// Kerberoastable users that are admins somewhere
MATCH (u:User {hasspn:true})-[:AdminTo]->(c:Computer) RETURN u,c
```

## Resources

- [BloodHound CE docs](https://bloodhound.specterops.io/)
- [SpecterOps - BloodHound 101](https://posts.specterops.io/)

## Related

- [Active Directory Basics](Active%20Directory%20Basics.md)
