GDPR stands for General Data Protection Regulation. It is a comprehensive data protection law enacted by the European Union (EU) to safeguard the privacy and personal data of individuals. Here's a simplified overview:

# What is GDPR?
- Purpose:

  - GDPR is designed to protect the fundamental rights and freedoms of individuals concerning the processing of their personal data.
- Applicability:

  - It applies to organizations, regardless of their location, that process the personal data of individuals residing in the European Union.
  - Organizations outside the EU must comply if they offer goods or services to EU residents or monitor their behavior.
- Key Principles:

  - Lawfulness, Fairness, and Transparency: Data processing must be lawful, fair, and transparent to the individuals whose data is being processed.
  - Purpose Limitation: Data should only be collected for specified, explicit, and legitimate purposes and not further processed in a manner incompatible with those purposes.
  - Data Minimization: Collect only the data that is necessary for the intended purpose.
  - Accuracy: Ensure that personal data is accurate and up-to-date.
  - Storage Limitation: Personal data should not be kept for longer than necessary.
  - Integrity and Confidentiality: Implement appropriate security measures to protect personal data from unauthorized or unlawful processing.
- Individual Rights:

  - GDPR grants individuals certain rights over their personal data, including the right to access, rectify, erase, restrict processing, and data portability.
  - Individuals also have the right to object to the processing of their personal data.
- Consent:

  - Organizations must obtain clear and explicit consent from individuals before processing their personal data.
  - Consent should be freely given, specific, informed, and unambiguous.
 - Data Breach Notification:

  - Organizations are required to report data breaches to the relevant supervisory authority without undue delay and, where feasible, within 72 hours of becoming aware of the breach.
- Data Protection Officer (DPO):

  - Some organizations are required to appoint a Data Protection Officer to oversee GDPR compliance, particularly if they process large amounts of sensitive data.
- Extraterritorial Reach:

  - GDPR has a global reach, and organizations outside the EU must comply if they handle the data of EU residents.
- Penalties:

  - Non-compliance with GDPR can result in significant fines, up to 4% of the global annual turnover of the organization or €20 million, whichever is higher.

## Why is GDPR Important?
- Privacy Protection: GDPR enhances the protection of individuals' privacy and gives them more control over their personal data.
- Accountability: Organizations are accountable for the way they handle and process personal data, fostering a culture of responsibility.
- Trust and Reputation: Compliance with GDPR builds trust with customers and enhances an organization's reputation for respecting privacy.
- Global Impact: GDPR has influenced data protection laws globally, as many countries have adopted or adapted similar regulations.
- Data Security: The regulation encourages organizations to implement robust security measures to protect personal data, reducing the risk of data breaches.


In summary, GDPR is a set of rules and regulations designed to protect the privacy and rights of individuals in the European Union regarding the processing of their personal data. It places a strong emphasis on transparency, accountability, and individual control over personal information.

---

## GDPR from a pentester's view

**Article 32** requires "appropriate technical and organisational measures" including *"a process for regularly testing, assessing and evaluating the effectiveness"* of security — which is a direct invitation for penetration testing.

### What a GDPR-focused pentest looks for

The goal is protecting **personal data (PII)** — find every way it could be exposed or exfiltrated.

```
- Where is PII stored/processed? (map data flows)
- Access control on PII — IDOR/BOLA exposing other users' data (very common, very reportable)
- Encryption at rest & in transit (Art. 32)
- Excessive data collection/retention (data minimization principle)
- Data exposure via APIs, backups, logs, error messages
```

### Breach angle (why findings carry weight)

- GDPR's **72-hour breach notification** and fines up to **€20M or 4% of global turnover** make any PII-exposure finding *business-critical*, not just technical.
- Demonstrating that you could exfiltrate personal data turns a "Medium" technical bug into a board-level risk — frame it that way in the [report](../Methodology/Report%20Writing.md).

### Handle data ethically

You may access real personal data during testing. The [Rules of Engagement](../Methodology/Rules%20of%20Engagement.md) must define how you handle, store, and destroy it — mishandling it could itself be a GDPR violation.

## Related

- [Broken Access Control](../OWASP%20Top%2010/Broken%20Access%20Control.md) · [Cryptographic Failures](../OWASP%20Top%2010/Cryptographic%20Failures.md)
- [ISO 27001](ISO%2027001.md) · [Rules of Engagement](../Methodology/Rules%20of%20Engagement.md)
