# What is CVE?
CVE stands for Common Vulnerabilities and Exposures. In the vast world of computer systems, software, and networks, vulnerabilities or weaknesses can exist. These vulnerabilities could potentially be exploited by attackers to compromise the integrity, confidentiality, or availability of information. To address this, a standardized system was created to uniquely identify and track these vulnerabilities. This system is known as CVE.

## Key Points:
- Identification: CVE provides a standardized way of identifying and naming vulnerabilities in software and hardware.
- Uniqueness: Each vulnerability is assigned a unique identifier called a CVE ID. This ID remains the same regardless of the product or vendor affected.
- Details: CVE entries include details about the vulnerabilities, such as a description of the issue, its severity, and any relevant references.
- Collaboration: The CVE system encourages collaboration between security researchers, vendors, and the broader cybersecurity community. It facilitates communication and information sharing.
- International Standard: CVE is an internationally recognized standard maintained by the MITRE Corporation, a not-for-profit organization that operates Federally Funded Research and Development Centers (FFRDCs) in the United States.

## How CVE Works:
- Discovery: Security researchers, vendors, or users discover a vulnerability in a product or system.
- Assignment of CVE ID: The researcher or organization assigns a CVE ID to the vulnerability.
- CVE Entry: A detailed entry is created in the CVE database, including information about the vulnerability, its impact, and any relevant fixes or mitigations.
- Public Disclosure: Once the vulnerability is properly documented, the information is made public. This disclosure encourages affected parties to take necessary actions to address the vulnerability.
- Updates and Fixes: Vendors release updates, patches, or fixes to address the identified vulnerabilities, securing the affected software or hardware.

## Importance of CVE:
- Standardization: CVE provides a standardized language for discussing and addressing vulnerabilities, reducing confusion and improving communication in the cybersecurity community.
- Prioritization: Organizations and individuals can use CVE information to prioritize the patching or mitigation of vulnerabilities based on severity and potential impact.
- Awareness: CVE increases awareness of potential threats and vulnerabilities, fostering a proactive approach to cybersecurity.

In summary, CVE is a crucial system in the realm of cybersecurity, providing a structured and standardized way to identify, track, and address vulnerabilities in software and hardware. It plays a vital role in facilitating collaboration and information sharing within the cybersecurity community.

---

## Using CVEs in a pentest

Your workflow is: **fingerprint versions → map to CVEs → find/verify an exploit.**

```bash
# 1. Fingerprint software and versions
nmap -sV target                       # service versions
whatweb https://target.com            # web tech stack
nmap --script vulners -sV target      # nmap maps versions to CVEs automatically

# 2. Search for exploits by CVE or product/version
searchsploit apache 2.4.49
searchsploit --cve CVE-2021-41773

# 3. Automated CVE scanning
nuclei -u https://target.com -t cves/

# 4. Look up details
# - https://nvd.nist.gov/vuln/detail/CVE-XXXX-XXXXX  (NVD, includes CVSS)
# - https://cve.mitre.org
```

### Prioritize with real-world signals

- **CISA KEV catalog** — CVEs *known to be actively exploited* (patch these first).
- **EPSS score** — probability a CVE will be exploited.
- **Public PoC available?** — dramatically raises real risk.

### Reporting tip

Always cite the CVE ID **and** confirm exploitability in the target's context — a high-CVSS CVE that isn't reachable or is already mitigated is not a critical finding.

## Related

- [CVSS](CVSS.md)
- [Vulnerable and Outdated Components](../OWASP%20Top%2010/Vulnerable%20and%20Outdated%20Components.md) · [SCA](../Security%20Testing%20Approaches/SCA.md)
