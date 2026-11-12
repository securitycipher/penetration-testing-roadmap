# Social Engineering Basics

Social engineering is hacking people instead of machines - manipulating someone into giving access, information, or a specific action. It is often the easiest way into an organization, which is why it is a core part of real [red team engagements](../Red%20Teaming/Red%20Team%20Methodology.md). The tech is only half the job; humans are the other attack surface.

## Why it works (principles of influence)

Attackers lean on predictable human tendencies (Cialdini's principles):

- **Authority** - people comply with perceived authority ("this is IT, we need your password reset")
- **Urgency / scarcity** - "your account will be locked in 10 minutes"
- **Social proof** - "everyone on your team already did this"
- **Liking / familiarity** - rapport and shared context lower defenses
- **Reciprocity** - a small favor creates a sense of obligation
- **Fear** - a threat pushes people to act before thinking

## Common techniques

- **Phishing** - email lures at scale (see [Phishing](Phishing.md))
- **Spear phishing** - targeted, personalized to one person
- **Vishing** - voice/phone pretexting (help desk resets, MFA fatigue by phone)
- **Smishing** - SMS-based lures
- **Pretexting** - inventing a believable scenario and identity
- **Baiting** - dropped USB drives, "free" downloads
- **Tailgating / piggybacking** - following staff through a secure door
- **Impersonation** - posing as a vendor, courier, or new employee on-site

## The engagement flow

1. **OSINT** - map the org, people, tech, and tone (see [OSINT Basics](../Recon/OSINT%20Basics.md))
2. **Pretext design** - build a believable story and identity
3. **Delivery** - email, call, SMS, or in person
4. **Exploitation** - capture credentials, drop a payload, or get the action
5. **Report** - what worked, click/report rates, and awareness gaps

## Ethics and scope

Social engineering is intrusive and personal. Stay strictly within written [rules of engagement](../Methodology/Rules%20of%20Engagement.md): approved targets only, no real harm, no illegal pretexts (impersonating law enforcement, etc.), and a clear deconfliction contact. The goal is to improve defenses, not to embarrass staff.

## Defense (for the report)

- Security-awareness training with realistic simulations
- Phishing-resistant MFA and strong help-desk verification
- Easy reporting ("report phish" button) and a no-blame culture
- Physical controls: badge checks, visitor escorts, clean-desk policy

## Related

- [Phishing](Phishing.md), [OSINT Basics](../Recon/OSINT%20Basics.md), [Red Team Methodology](../Red%20Teaming/Red%20Team%20Methodology.md)
