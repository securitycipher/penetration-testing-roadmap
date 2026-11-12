# Responder

Responder is the classic tool for poisoning name-resolution protocols on a Windows network. When a Windows host fails to resolve a name via DNS, it falls back to **LLMNR, NBT-NS, and mDNS** - broadcast protocols with no authentication. Responder answers those broadcasts, says "that's me," and captures the authentication the victim sends. It is usually the first thing you run on an internal engagement.

## What it does

- Poisons **LLMNR / NBT-NS / mDNS** to capture **NetNTLM** hashes
- Runs rogue servers (SMB, HTTP, LDAP, etc.) to harvest credentials
- Feeds directly into [NTLM relay](../Active%20Directory/NTLM%20Relay.md) attacks
- Captured NetNTLMv2 hashes can be cracked offline with [Hashcat](Hashcat.md)

## Basic usage

```bash
# Listen and poison on your interface
sudo responder -I eth0

# Analyze-only mode: watch what's happening without poisoning (safe recon first)
sudo responder -I eth0 -A
```

Captured hashes land in `/usr/share/responder/logs/` (and are printed live).

## Crack what you capture

```bash
hashcat -m 5600 netntlmv2.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

Mode 5600 is NetNTLMv2. A cracked hash gives you a valid domain credential to start pivoting.

## Capture vs relay

- **Capture + crack** - good when the account has a weak password.
- **Relay** - when you cannot crack it, forward the auth to another host with [NTLM relay](../Active%20Directory/NTLM%20Relay.md) (turn off Responder's SMB/HTTP servers so `ntlmrelayx` can bind those ports).

## OPSEC note

Responder is **noisy** and easy for defenders to spot (it answers for names that do not exist). Start in analyze mode, and on a red team prefer targeted poisoning over blasting the whole subnet.

## Mitigation (for the report)

- Disable LLMNR and NBT-NS via Group Policy; disable mDNS where possible.
- Enforce SMB signing to break relay.
- Alert on hosts answering LLMNR/NBT-NS broadcasts.

## Resources

- [Responder on GitHub](https://github.com/lgandx/Responder)
- [NetExec](NetExec.md), [Impacket](Impacket.md), [NTLM Relay](../Active%20Directory/NTLM%20Relay.md)
