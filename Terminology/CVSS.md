# Common Vulnerability Scoring System (CVSS)
The Common Vulnerability Scoring System (CVSS) is a framework used to assess and communicate the severity of security vulnerabilities in software systems. It provides a standardized method for evaluating vulnerabilities, allowing security professionals to prioritize their response efforts effectively. CVSS scores help organizations understand the potential impact of a vulnerability and make informed decisions about how to mitigate it.

## Here's a breakdown of the key components of the CVSS

- Base Score: The base score represents the intrinsic qualities of a vulnerability and is calculated using several metrics:

  - Attack Vector (AV): This metric describes how an attacker can exploit the vulnerability. For example, is physical access required, or can it be exploited remotely over a network?

  - Attack Complexity (AC): This metric considers how complex the attack is to execute. Is it straightforward or requires significant resources or conditions?

  - Privileges Required (PR): This metric indicates the level of privileges an attacker needs to exploit the vulnerability. Does the attacker require elevated privileges, or can it be exploited with minimal access?

  - User Interaction (UI): This metric assesses whether the vulnerability can be exploited without user interaction. Does the attacker need to trick the user into performing an action, or can it be exploited silently?

  - Scope (S): This metric determines whether the vulnerability impacts the system where it's located or can affect other systems. Is the vulnerability confined to the vulnerable component, or can it extend to other parts of the system?

- Temporal Score: The temporal score reflects the characteristics of a vulnerability that may change over time. It includes metrics like exploit code maturity, remediation level, and report confidence. These factors can influence the urgency of patching or mitigating the vulnerability.

- Environmental Score: The environmental score allows organizations to customize the CVSS score based on their specific deployment environment. Factors such as the importance of the affected asset, the sensitivity of the data it handles, and the security controls in place can all affect the overall risk posed by the vulnerability.

The CVSS score is represented as a numeric value between 0.0 and 10.0, with higher scores indicating more severe vulnerabilities. Organizations can use these scores to prioritize their response efforts, focusing on vulnerabilities with the highest potential impact on their systems.

It's important to note that while CVSS provides a valuable framework for assessing vulnerabilities, it's just one tool in the broader cybersecurity toolbox. Organizations should consider other factors, such as threat intelligence, asset criticality, and business impact, when making decisions about vulnerability management and remediation.

---

## Scoring findings as a pentester

You'll assign a CVSS score to every finding in your [report](../Methodology/Report%20Writing.md). Use the official calculator and reason through each vector.

```
Calculator: https://www.first.org/cvss/calculator/3.1

Vector string example:
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H = 9.8 (Critical)
```

### Severity bands (CVSS v3.1)

| Score | Severity |
|-------|----------|
| 0.0 | None |
| 0.1–3.9 | Low |
| 4.0–6.9 | Medium |
| 7.0–8.9 | High |
| 9.0–10.0 | Critical |

### Worked examples

- **Unauth RCE (internet-facing):** `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H` → **9.8 Critical**
- **Stored XSS (needs a logged-in victim):** `AV:N/AC:L/PR:L/UI:R/S:C/C:L/I:L/A:N` → ~**5.4 Medium**
- **IDOR reading other users' data:** `AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:N/A:N` → **6.5 Medium**

### Base vs Temporal vs Environmental

- **Base** = the intrinsic severity (what most reports quote).
- **Temporal** = adjusts for exploit maturity/patch availability over time.
- **Environmental** = adjusts for *this* client (asset criticality, existing controls) — use it to explain why a "High" is actually "Critical" for them.

### Don't let the number override judgment

A "Medium" IDOR that exposes every customer's financial records may matter more to the business than a "High" with no real exploit path. Pair CVSS with business impact.

## Related

- [CVE](CVE.md) · [Report Writing](../Methodology/Report%20Writing.md)




