# CRTP (Certified Red Team Professional)

CRTP from **Altered Security** (formerly delivered under the Pentester Academy brand) focuses on **Active Directory attacks** in a realistic lab. It is the standard follow-up after OSCP for internal and red-team work, and it is well known for strong value per dollar.

## What it covers

- AD enumeration with PowerView and [BloodHound](../Active%20Directory/BloodHound.md)
- [Kerberoasting](../Active%20Directory/Kerberoasting.md), AS-REP roasting, delegation abuse
- Lateral movement (PSRemoting, WMI, DCOM)
- Domain persistence (Golden Ticket, Silver Ticket, [DCSync](../Active%20Directory/DCSync.md))
- Cross-trust attacks and basic AV/EDR evasion

## Prerequisites

- Comfortable with Windows and Linux
- Basic AD knowledge helps, but the course teaches from the ground up
- OSCP-level hands-on experience is useful, not required

## Exam format

- **24-hour** practical lab exam, then 48 hours to submit the report
- Compromise the domain(s) and show proof, plus a written report
- Real AD lab, not CTF-style puzzles

## Resources

- [Altered Security CRTP](https://www.alteredsecurity.com/gcb)
- [Game of Active Directory (GOAD)](../Labs/GOAD.md) for free home practice

## Career path

CRTP -> **CRTE** (advanced AD) -> **CRTO** or red-team roles and AD-focused pentest lead.
