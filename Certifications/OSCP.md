# OSCP / OSCP+ (Offensive Security Certified Professional)

OSCP is the gold-standard hands-on offensive cert from OffSec. Since the November 2024 exam change, passing the current exam awards **OSCP+**, a version that **expires after 3 years** unless you renew it. The classic lifetime "OSCP" is still what people mean in job ads, and OSCP+ satisfies it.

## Course and exam

- Course: **PEN-200** (Penetration Testing with Kali Linux). The old "PWK" name is retired.
- Exam: **23 hours 45 minutes** hands-on, then 24 hours to submit a professional report.
- Targets: 3 standalone machines plus a small **Active Directory set** (now "assumed breach" - you get a foothold and must own the domain).
- **Bonus points were removed** in the 2024 update. You pass purely on exam machine points (70/100), and the AD set must be fully completed to pass comfortably.

## What it covers

- Enumeration, service exploitation, and manual web attacks
- Linux and Windows privilege escalation
- Active Directory: Kerberoasting, lateral movement, domain compromise
- Buffer-basics and public exploit modification (no heavy exploit dev)
- Clear, reproducible report writing

## How to prepare

- Grind the PEN-200 labs, then **Proving Grounds Practice** and **HackTheBox** boxes
- Practice AD attacks (see [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md))
- Keep a repeatable methodology and note-taking template before exam day

## Resources

- [OffSec PEN-200 / OSCP](https://www.offsec.com/courses/pen-200/)
- [Proving Grounds](https://www.offsec.com/labs/)

## Compare

- **eJPT / PNPT** - gentler on-ramps before OSCP
- **OSCP+** - the broad offensive benchmark
- **OSEP / CPTS** - natural next steps for deeper/red-team work
