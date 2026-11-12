# Red Team Methodology

Red teaming is adversary simulation: instead of finding every bug (a pentest), you emulate a specific threat actor to test whether an organization can **detect and respond** to a realistic attack. The deliverable is not a vulnerability list - it is the story of how far an attacker got, how quietly, and what the blue team saw. This is the mindset behind certs like [CRTO](../Certifications/CRTO.md) and [OSEP](../Certifications/OSEP.md).

## Red team vs pentest

- **Pentest:** broad coverage, find and report as many issues as possible, usually noisy, defenders often know.
- **Red team:** narrow objective (for example "reach the payroll database"), stay undetected, test people and process, not just tech.

## The engagement lifecycle

1. **Objectives and rules of engagement** - crown jewels, scope, deconfliction, legal (see [Rules of Engagement](../Methodology/Rules%20of%20Engagement.md)).
2. **Recon / OSINT** - map people, tech, and exposure (see [OSINT Basics](../Recon/OSINT%20Basics.md)).
3. **Initial access** - [phishing](../Social%20Engineering/Phishing.md), exposed services, or a valid credential.
4. **Establish C2** - a reliable, OPSEC-safe callback (see [Command and Control](Command%20and%20Control%20(C2).md)).
5. **Situational awareness** - enumerate host, users, and domain quietly.
6. **Privilege escalation** - local then domain (see [Privilege Escalation](../Vulnerabilities/Privilege%20Escalation.md)).
7. **Lateral movement** - pivot toward the objective (see [Pass-the-Hash](../Active%20Directory/Pass-the-Hash.md)).
8. **Persistence** - survive reboots and credential resets.
9. **Actions on objective** - prove access to the target asset.
10. **Reporting + purple team** - replay the attack with defenders to improve detections.

## The attacker's loop

Most of the engagement is a repeating cycle: **enumerate -> escalate -> move -> persist**, mapped to phases of [MITRE ATT&CK](MITRE%20ATT%26CK.md). Each action is weighed against OPSEC: what logs or alerts does it generate?

## OPSEC mindset

- Assume [EDR](AV%20and%20EDR%20Evasion.md) and a SOC are watching ([SIEM](../Terminology/SIEM.md)).
- Prefer living-off-the-land and built-in tools over dropping binaries.
- Minimize noise: no mass scanning, no default tool signatures, no unnecessary actions.
- Log your own actions with timestamps for the deconfliction and purple-team phase.

## Frameworks

- **MITRE ATT&CK** - the common language for techniques (see [MITRE ATT&CK](MITRE%20ATT%26CK.md))
- **Cyber Kill Chain** (Lockheed Martin) - higher-level phase model
- **Unified Kill Chain** - combines both for end-to-end engagements

## Related

- [Command and Control (C2)](Command%20and%20Control%20(C2).md), [AV and EDR Evasion](AV%20and%20EDR%20Evasion.md), [MITRE ATT&CK](MITRE%20ATT%26CK.md)
- [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md)
