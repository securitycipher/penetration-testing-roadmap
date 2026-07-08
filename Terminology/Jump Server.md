# Jump Server
A Jump Server, sometimes also referred to as a Jump Host or Jump Box, is a special-purpose computer on a network used to securely access and manage other devices, typically servers, within that network. It acts as an intermediary or gateway, allowing authorized users to connect to target systems without directly accessing them from outside the network.

## Here's a simplified explanation of how it works and why it's important

- Secure Access Point: Imagine you have a house with multiple rooms, each containing valuable items. You don't want just anyone walking into those rooms and handling the items. So, you install a security door at the entrance, and only authorized personnel with the right keys can enter.

- Authentication and Authorization: The Jump Server acts as that security door. When someone wants to access one of the servers inside the network, they first connect to the Jump Server. Here, they must authenticate themselves, proving they have the right credentials to enter. Once verified, they're granted access to the target system they need to manage.

- Control and Monitoring: The Jump Server allows administrators to enforce security policies more effectively. They can monitor who accesses which servers, track activities, and ensure that only approved actions are performed.

- Reduced Attack Surface: By funneling all remote access through a single point (the Jump Server), organizations can reduce the number of entry points into their network. This minimizes the risk of unauthorized access and makes it easier to implement and manage security measures.

- Additional Security Measures: Advanced Jump Server setups often include additional security measures like multi-factor authentication, session recording, and encryption to further safeguard sensitive data and systems.

- Compliance Requirements: Many industries have strict compliance regulations regarding access control and data security. Using a Jump Server can help organizations meet these requirements by providing a centralized and auditable access point.

In summary, a Jump Server is like a checkpoint that ensures only authorized users can access critical systems within a network. It enhances security, simplifies management, and helps organizations comply with regulations, all while providing a more controlled and monitored environment for remote access.

---

## Jump servers from a pentester's view

A jump server is a **high-value target and a chokepoint**. Because *all* admin access funnels through it, compromising it can hand you the keys to the whole internal estate — and it's also a natural **pivot point**.

### Why attackers love it

- It has **network routes to sensitive systems** other hosts can't reach.
- It often holds **cached credentials, SSH keys, and RDP sessions** of admins.
- It's a legitimate hop, so lateral movement *from* it looks normal.

### Post-compromise actions

```bash
# Harvest SSH keys and known_hosts (maps the internal network for you)
find / -name id_rsa 2>/dev/null
cat ~/.ssh/known_hosts

# Hijack existing SSH agent / control sockets
ls -la ~/.ssh/  ; env | grep SSH_AUTH_SOCK

# Windows jump box: dump cached creds / active sessions
mimikatz # sekurlsa::logonpasswords
query user   # see other admins' RDP sessions to hijack
```

### Attacking / hardening checks

- **MFA on the jump host?** If not, stolen admin creds = full pivot access.
- **Session recording / logging?** (You may be watched — relevant for stealth.)
- **Outbound restrictions?** A properly locked-down jump box only reaches specific targets on specific ports.
- **Shared local admin password** across jump and target hosts (Pass-the-Hash risk).

A jump server *concentrates* risk: great for the defender's monitoring, but catastrophic for them if you own it.

## Related

- [DMZ](DMZ.md) · [Session Hijacking](../Vulnerabilities/Session%20Hijacking.md)
- [MFA vs 2FA](MFA%20vs%202FA.md) · [Lateral Movement](../Active%20Directory/Active%20Directory%20Basics.md)
