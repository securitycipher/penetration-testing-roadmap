# Phishing

Phishing is sending a lure (usually email) to trick someone into clicking a link, opening an attachment, entering credentials, or approving an [MFA](../Terminology/MFA%20vs%202FA.md) prompt. It is the most common initial-access technique in real attacks and red-team engagements, so testing an org's susceptibility and response is high value.

## Types

- **Mass phishing** - generic lure to many recipients
- **Spear phishing** - tailored to a specific person using [OSINT](../Recon/OSINT%20Basics.md)
- **Whaling** - targeting executives
- **Credential harvesting** - fake login page that captures username/password
- **MFA phishing** - a reverse proxy (Evilginx-style) that relays the login and steals the **session cookie**, defeating app-based MFA
- **MFA fatigue** - spamming push approvals until the user accepts
- **Payload delivery** - attachment or link that drops a [C2](../Red%20Teaming/Command%20and%20Control%20(C2).md) implant

## Building a credible campaign

1. **Recon** - find email format, names, tech stack, and tone (see [OSINT Basics](../Recon/OSINT%20Basics.md)).
2. **Infrastructure** - a look-alike domain, valid TLS, and good sender reputation (SPF/DKIM/DMARC set up so you land in the inbox).
3. **Pretext** - a believable reason to act now (password expiry, shared document, HR notice).
4. **Landing page** - a convincing clone, or a proxy for MFA theft.
5. **Tracking** - who opened, clicked, and submitted, and who reported it.

## Tools

- **GoPhish** - open-source phishing campaign framework (templates, tracking, landing pages)
- **Evilginx** - reverse-proxy phishing for session/MFA token theft (authorized testing only)
- **King Phisher**, **SET** (Social-Engineer Toolkit)
- Domain + mail setup: look-alike domain, categorized domain, warmed sender

## Measuring success

Report the real signal, not just clicks: open rate, click rate, credential-submit rate, payload-execution rate, and critically the **report rate and time-to-report**. Fast reporting is a sign of a healthy security culture.

## Defense (for the report)

- Phishing-resistant MFA (FIDO2/passkeys) to kill credential and token theft
- DMARC enforcement, external-sender banners, link rewriting/sandboxing
- Regular simulations plus a one-click report button and no-blame follow-up
- Help-desk verification procedures to stop vishing resets

## Related

- [Social Engineering Basics](Social%20Engineering%20Basics.md), [OSINT Basics](../Recon/OSINT%20Basics.md)
- [Command and Control (C2)](../Red%20Teaming/Command%20and%20Control%20(C2).md)
