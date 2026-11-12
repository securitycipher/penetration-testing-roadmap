# AD CS Attacks (ESC1-ESC16)

Active Directory Certificate Services (AD CS) is Microsoft's PKI for issuing certificates inside a domain. It is wildly common and frequently misconfigured. Since the "Certified Pre-Owned" research, a set of escalation paths labelled **ESC1 through ESC16** has made AD CS one of the most reliable ways to go from a normal user to Domain Admin. If a domain runs AD CS, always check it.

## Why certificates are powerful

A certificate that allows client authentication can be used to authenticate as the subject it was issued for - often without a password, and it keeps working even after the password changes. Get a cert for a Domain Admin and you effectively are one.

## The common escalation paths

- **ESC1** - a template lets low-priv users enroll *and* supply an arbitrary Subject Alternative Name (SAN). Request a cert "as" the domain admin.
- **ESC2** - template with the Any Purpose EKU (or no EKU), usable for authentication.
- **ESC3** - abuse of the Certificate Request Agent (enrollment agent) EKU to request on behalf of others.
- **ESC4** - you have write/control over a template's ACL, so you reconfigure it into an ESC1.
- **ESC6** - the CA has `EDITF_ATTRIBUTESUBJECTALTNAME2` set, allowing SAN injection on any template.
- **ESC7** - control over the CA itself (ManageCA / ManageCertificates rights).
- **ESC8** - **NTLM relay to the CA web enrollment (HTTP)** endpoint; relay a machine account and get a cert (pairs with [NTLM Relay](NTLM%20Relay.md)).
- **ESC9 / ESC10** - weak certificate mapping (`szOID` / `altSecurityIdentities`) enabling impersonation.
- **ESC11** - relay to the CA's RPC enrollment interface.
- **ESC13** - certificate issuance policy linked to a privileged group.
- **ESC15 / ESC16** - newer application-policy and mapping flaws (CVE-2024-era). Keep Certipy current.

## Finding and exploiting with Certipy

```bash
# Enumerate templates and flag vulnerable ones
certipy find -u user@domain.tld -p 'Password1' -dc-ip 10.0.0.1 -vulnerable -stdout

# ESC1: request a cert with an arbitrary SAN (become the admin)
certipy req -u user@domain.tld -p 'Password1' -ca CA-NAME \
  -template VulnTemplate -upn administrator@domain.tld

# Use the cert to get a TGT and the NT hash
certipy auth -pfx administrator.pfx -dc-ip 10.0.0.1
```

Certipy ships the escalation checks and can auto-exploit most ESCs. [BloodHound](BloodHound.md) CE also surfaces several AD CS paths.

## Mitigation

- Audit templates: remove "enrollee supplies subject" unless required; restrict enroll rights.
- Disable `EDITF_ATTRIBUTESUBJECTALTNAME2` (ESC6).
- Enforce certificate mapping hardening (the 2022/2023 KBs) and strong mapping.
- Enable **Extended Protection for Authentication** and require HTTPS on enrollment endpoints (ESC8/ESC11).
- Monitor certificate issuance for unexpected SANs.

## Related

- [NTLM Relay](NTLM%20Relay.md) (ESC8/ESC11 depend on it)
- [DCSync](DCSync.md), [Kerberoasting](Kerberoasting.md)
- [Active Directory Basics](Active%20Directory%20Basics.md)
