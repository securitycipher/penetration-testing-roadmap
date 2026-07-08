# STRIDE
STRIDE is a threat modeling framework that helps in identifying and mitigating security threats in software systems. It was introduced by Microsoft to assist developers and security professionals in understanding and addressing potential vulnerabilities early in the software development life cycle. The name "STRIDE" is an acronym representing six different types of security threats:

- Spoofing Identity:
  - Definition: This threat involves attackers pretending to be someone else by using false identities.
  - Example: A malicious user gaining unauthorized access to a system by pretending to be an authenticated user.

- Tampering with Data:
  - Definition: This threat refers to the unauthorized modification or alteration of data.
  - Example: An attacker manipulating the data being transmitted between a client and a server to disrupt or corrupt the communication.

- Repudiation:
  - Definition: Repudiation threats involve actions taken by users that are later denied.
  - Example: A user making a financial transaction and later denying that they initiated it, leading to potential disputes.

- Information Disclosure:
  - Definition: This threat involves the exposure of sensitive information to unauthorized parties.
  - Example: A flaw in a system allowing an attacker to access confidential user data or financial information.

- Denial of Service (DoS):
  - Definition: Denial of Service attacks aim to make a system or service unavailable to its users.
  - Example: Flooding a website with excessive traffic to the point where legitimate users can no longer access it.

- Elevation of Privilege:
  - Definition: This threat involves unauthorized users gaining higher levels of access or privileges.
  - Example: Exploiting a vulnerability to elevate user privileges, allowing an attacker to gain administrative control over a system.

Using the STRIDE framework, security professionals and developers can systematically analyze each aspect of a system to identify potential threats and vulnerabilities. Once identified, appropriate countermeasures and security controls can be implemented to mitigate these risks. The goal is to ensure that security considerations are an integral part of the software development process, promoting a proactive approach to building secure and robust systems.

---

## STRIDE mapped to real attacks and controls

Each STRIDE category maps directly to vulnerabilities you test for as a pentester and the control that stops them.

| STRIDE | Violates | Example attack | Test with | Control |
|--------|----------|----------------|-----------|---------|
| **S**poofing | Authentication | Session hijacking, credential stuffing, JWT `alg:none` | [Session Hijacking](../Vulnerabilities/Session%20Hijacking.md) | MFA, strong session mgmt |
| **T**ampering | Integrity | Parameter tampering, request forgery | Burp Repeater | Input validation, signing/HMAC |
| **R**epudiation | Non-repudiation | Deleting/forging logs | Log review | Audit logging, [Digital Signatures](../Cryptography/Digital%20Signature.md) |
| **I**nformation disclosure | Confidentiality | IDOR, verbose errors, [SQLi](../Vulnerabilities/SQL%20Injection.md) | [Broken Access Control](../OWASP%20Top%2010/Broken%20Access%20Control.md) | Encryption, access control |
| **D**enial of service | Availability | Resource exhaustion, [request smuggling](../Vulnerabilities/HTTP%20Request%20Smuggling.md) | Load/fuzz testing | Rate limiting, quotas |
| **E**levation of privilege | Authorization | [Privilege escalation](../Vulnerabilities/Privilege%20Escalation.md), broken RBAC | [Broken Access Control](../OWASP%20Top%2010/Broken%20Access%20Control.md) | Least privilege, authZ checks |

## How to apply STRIDE (quick workflow)

1. **Draw a data-flow diagram** — actors, processes, data stores, trust boundaries.
2. **Walk each element** and ask "which STRIDE threats apply here?"
3. **Focus on trust boundaries** — where data crosses from untrusted to trusted is where most threats live.
4. **Record threat → likelihood/impact → mitigation.**

## Related

- [Threat modeling](Threat%20modeling.md) · [PASTA](PASTA.md)
- [OWASP Top 10](../OWASP%20Top%2010/OWASP%20Top%2010.md)
