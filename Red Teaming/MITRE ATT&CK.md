# MITRE ATT&CK

MITRE ATT&CK is a free, globally used knowledge base of real-world adversary **tactics, techniques, and procedures (TTPs)**. It is the common language that red teams, blue teams, and threat intel all share: when you say "we used T1558.003," everyone knows you mean Kerberoasting. Learning ATT&CK makes your findings map cleanly to how defenders think.

## The structure

- **Tactics** - the attacker's goal (the "why"). 14 enterprise tactics, roughly in order:
  Reconnaissance, Resource Development, Initial Access, Execution, Persistence, Privilege Escalation, Defense Evasion, Credential Access, Discovery, Lateral Movement, Collection, Command and Control, Exfiltration, Impact.
- **Techniques** - how a goal is achieved (the "how"), e.g. T1566 Phishing.
- **Sub-techniques** - specific variants, e.g. T1566.001 Spearphishing Attachment.
- **Procedures** - the concrete implementation a given actor/tool uses.

## Matrices

- **Enterprise** (Windows, Linux, macOS, cloud, containers) - the one you will use most
- **Mobile** and **ICS** (industrial control systems) - specialized matrices

## How red teams use it

- **Scope and plan** an engagement around a specific threat actor's known TTPs.
- **Map your actions** so the report speaks the defender's language.
- **Measure coverage** - which techniques did detections catch, and which slipped through?

## How blue teams use it

- **Detection engineering** - build and tag alerts per technique.
- **Gap analysis** - find techniques with no coverage.
- **Threat intel** - track which groups use which TTPs.

## Example mapping (an AD attack chain)

| Step | Technique |
|------|-----------|
| Phishing email with macro | T1566.001 Spearphishing Attachment |
| Macro runs PowerShell | T1059.001 PowerShell |
| Dump cached creds | T1003 OS Credential Dumping |
| [Kerberoasting](../Active%20Directory/Kerberoasting.md) | T1558.003 |
| [Pass-the-Hash](../Active%20Directory/Pass-the-Hash.md) lateral move | T1550.002 |
| Beacon callback | T1071.001 Web Protocols (C2) |

## Related tools and frameworks

- **ATT&CK Navigator** - visualize coverage on the matrix
- **CALDERA** - automated adversary emulation built on ATT&CK
- **Atomic Red Team** - small tests per technique to validate detections
- For AI systems, the equivalent is **MITRE ATLAS** (see [AI Red Teaming](../OWASP%20Top%2010%20LLM/AI%20Red%20Teaming.md))

## Resources

- [MITRE ATT&CK](https://attack.mitre.org/)
- [Red Team Methodology](Red%20Team%20Methodology.md)
