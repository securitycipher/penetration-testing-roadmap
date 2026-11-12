# CRTO (Certified Red Team Operator)

CRTO from **Zero-Point Security** is the most popular entry into modern **red teaming**. It teaches adversary simulation with a real C2 framework and a focus on operating quietly against a defended Windows estate.

## What it covers

- Red-team methodology and operations planning
- Command and control with **Cobalt Strike** (the course provides lab access)
- Initial access, host recon, and privilege escalation
- [Active Directory](../Active%20Directory/Active%20Directory%20Basics.md) attacks and lateral movement
- **Defence evasion**: AV/EDR bypass, OPSEC, Windows Defender and ETW awareness
- Persistence and domain dominance

## Exam format

- **48-hour** hands-on lab exam (time is cumulative, you can pause)
- Objective: compromise a set number of hosts and capture flags in a defended domain
- Open-book; the challenge is operating against detections, not solving puzzles

## Who it is for

- Pentesters moving from "find all the bugs" to "simulate a real adversary"
- Anyone targeting red-team or purple-team roles
- A natural step after OSCP/CPTS and CRTP

## How to prepare

- Work through the Zero-Point Security course labs
- Build detection awareness (see [SIEM](../Terminology/SIEM.md)) so you understand what blue sees

## Resources

- [Zero-Point Security CRTO](https://training.zeropointsecurity.co.uk/courses/red-team-ops)

## Compare

- **CRTP** - AD attacks, cheaper, less evasion focus
- **CRTO** - full red-team ops with C2 and EDR evasion
- **OSEP** - OffSec's evasion-heavy alternative
