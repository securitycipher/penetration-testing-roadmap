# Frequently Asked Questions

## How long does it take to become a penetration tester?

With consistent part-time study (10-15 hours/week), most people reach junior pentester readiness in **6-12 months**. A focused full-time learner can compress foundations and web security into 3-4 months before starting cert prep.

## Do I need a computer science degree?

No. Employers care more about **labs, certs, and writeups** than degrees. Strong Linux, networking, and web fundamentals matter more than formal credentials.

## OSCP vs CEH vs CompTIA Security+?

- **Security+** - broad entry-level cert, good for HR filters and fundamentals.
- **CEH** - theory-heavy, widely recognized but less respected by hands-on teams.
- **OSCP / OSCP+** - gold standard for offensive roles; expect 200+ lab hours before attempting. Since late 2024 the exam awards OSCP+, which expires after 3 years.
- **eJPT / PNPT** - gentler on-ramps before OSCP.
- **CPTS / BSCP / CRTP** - strong, cheaper alternatives (full pentest, web, and Active Directory). See the [Certifications overview and comparison matrix](Certifications/README.md) for a cert-by-goal breakdown.

## Is bug bounty the same as penetration testing?

Not exactly. Bug bounty is **continuous, scope-limited, reward-based** hunting on public programs. Pentesting is a **time-boxed engagement** with a formal report and rules of engagement. Skills overlap heavily; recon and web testing transfer directly.

## What should I learn first?

Linux command line, networking (TCP/IP, DNS, HTTP), and one scripting language (Python or Bash). Then start [PortSwigger Web Security Academy](https://portswigger.net/web-security) labs in parallel with TryHackMe's beginner paths.

## Are these tools legal to use?

Only on systems you own or have **written authorization** to test. Unauthorized scanning or exploitation is illegal. Always get scope and rules of engagement before testing.

## How do I get started with AI & LLM application security testing?

Large Language Model (LLM) security is one of the fastest-growing attack surfaces in enterprise pentesting:
- **Core Vulnerabilities:** Focus on Prompt Injection (direct/indirect), Insecure Output Handling (XSS/RCE via model responses), Training Data Poisoning, and Sensitive Information Disclosure via RAG (Retrieval-Augmented Generation).
- **Standards:** Study the **OWASP Top 10 for LLM Applications and Generative AI** - it is the definitive framework for assessing AI implementations.
- **Hands-on Practice:**
  - [Lakera Gandalf](https://gandalf.lakera.ai/) - A gamified platform to practice prompt injection and guardrail evasion across progressive difficulty levels.
  - [PortSwigger Web Security Academy LLM Attacks](https://portswigger.net/web-security/llm-attacks) - Hands-on labs simulating real web applications backed by vulnerable LLM integrations.

## Why is Active Directory essential for real-world pentests, and how can I practice at home?

Over 90% of enterprises run Windows Active Directory (or hybrid Entra ID) for identity and access management. Real internal network penetration tests rarely look like standalone Linux root challenges - they center on lateral movement, domain reconnaissance, and privilege escalation:
- **Key Concepts:** Kerberoasting, AS-REP roasting, DCSync, Pass-the-Hash, credential dumping (Mimikatz), and attack path visualization with [BloodHound](https://github.com/SpecterOps/BloodHound).
- **Free Home Practice:**
  - [Game of Active Directory (GOAD)](https://github.com/Orange-Cyberdefense/GOAD) - A free, automated vulnerable Active Directory lab you can deploy locally on VirtualBox or Proxmox.
  - TryHackMe / HackTheBox dedicated AD tracks (e.g., *Wreath*, *Dante*, and *Zephyr*).

## Does being good at CTFs make me ready for enterprise penetration testing?

CTFs are fantastic for building tool fluency (Burp Suite, Wireshark, nmap, Ghidra) and problem-solving reflexes, but **CTF is not equal to commercial pentesting**:
- **CTFs** are designed puzzles with guaranteed intentional flags and single-path vulnerabilities.
- **Commercial Pentests** assess enterprise architectures with hardened configurations, defense-in-depth, and EDR controls. The deliverable is not a flag, but an actionable, risk-rated **executive and technical remediation report**.
- **Bridging the Gap:** Transition from puzzle CTFs to multi-machine network labs (HTB Pro Labs, Proving Grounds), and practice writing thorough, professional pentest reports explaining root causes and defensive remediations.

## How do I build a standout portfolio to get hired without prior cybersecurity experience?

Hiring managers want tangible proof that you can think methodically and communicate findings clearly:
1. **High-Quality Technical Write-ups:** Document your lab walkthroughs and vulnerability research on a personal blog or GitHub repository. Focus on *why* the vulnerability existed, how you identified it, and *how to fix it*, rather than just pasting exploit scripts.
2. **Open-Source Tooling:** Build or contribute to security automation tools, recon scripts, or custom Burp extensions on GitHub. Clean, documented code immediately sets you apart from applicants who only possess credentials.
3. **Public Profiles:** Keep your TryHackMe, HackTheBox, or bug bounty profile links in your resume to demonstrate ongoing hands-on commitment.

## Offensive (Red) vs. Defensive (Blue/SOC): Which is better to break into?

While media glorifies offensive "Red Teaming", the hiring landscape strongly favors defensive roles:
- **Job Volume:** Entry-level SOC (Security Operations Center) Analyst, Threat Detection, and Incident Response openings outnumber junior pentester openings by roughly 10:1.
- **Foundation:** The best penetration testers deeply understand defensive logging, SIEMs, and EDR telemetry. Starting in a SOC gives you frontline exposure to real-world attack techniques and enterprise network realities, making a future transition to Red or Purple teaming far smoother.
