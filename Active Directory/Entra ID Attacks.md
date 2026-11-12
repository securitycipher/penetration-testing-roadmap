# Entra ID (Azure AD) Attacks

Entra ID (formerly Azure Active Directory) is Microsoft's **cloud** identity platform - the backbone of Microsoft 365 and Azure. Most enterprises now run a **hybrid** setup: on-prem [Active Directory](Active%20Directory%20Basics.md) synced to Entra ID. That sync, and the cloud-only features around it, open attack paths that classic AD testing misses. This is the focus of the [CARTP](../Certifications/CARTP.md) cert.

## Entra ID is not on-prem AD

- No Kerberos/NTLM or LDAP to the same degree; it is **OAuth 2.0 / OIDC / SAML** and REST (Microsoft Graph).
- Authorization is **role-based** (RBAC) across directory roles and Azure resource roles.
- Identities include users, **service principals**, **managed identities**, and **app registrations** - non-human identities are a huge, often-ignored attack surface.

## Recon and initial access

- **User/tenant enumeration** - validate emails and find the tenant without auth (AADInternals, o365creds techniques).
- **Password spraying** against Entra sign-in (watch Smart Lockout and Conditional Access).
- **Phishing for tokens** - device-code phishing and **OAuth consent phishing** (illicit consent grant) to steal tokens/refresh tokens instead of passwords.
- **Token theft** - steal primary refresh tokens (PRT) or access tokens from a compromised endpoint; MFA is already satisfied in a stolen token.

## Privilege escalation and abuse

- **App registration / service principal abuse** - add credentials to an app you can edit, then authenticate as it.
- **Managed identity abuse** - from a compromised Azure VM/function, grab a token from the metadata endpoint (IMDS) and use its roles.
- **Role escalation** - abuse high-value directory roles (Global Admin, Privileged Role Admin, Application Admin).
- **Consent grant** - trick an admin (or abuse your rights) to grant an app powerful Graph permissions.

## Hybrid: on-prem to cloud and back

- **Password Hash Sync (PHS)** - compromising the sync server (and the `MSOL_` account) can expose credentials.
- **Pass-through Authentication (PTA)** - a malicious agent can harvest plaintext logins.
- **Federation (ADFS) abuse** - steal the token-signing key and forge SAML tokens (the "Golden SAML" attack) to impersonate anyone in the cloud.
- **Entra Connect** sync server is effectively Tier 0 - owning it often means owning the tenant.

## Tools

- **ROADtools** (see [ROADtools](../Cloud/ROADtools.md)) - enumerate the whole directory
- **AzureHound** - feed Entra/Azure data into [BloodHound](BloodHound.md) for attack paths
- **AADInternals** - broad Entra/hybrid attack toolkit
- **MicroBurst**, **Pacu** (see [Pacu](../Cloud/Pacu.md)) for Azure/AWS resource attacks

## Mitigation

- Enforce phishing-resistant MFA and strict **Conditional Access**; monitor for token theft.
- Treat the **Entra Connect sync server as Tier 0**; protect `MSOL_` and sync accounts.
- Audit app registrations, service principal credentials, and consent grants regularly.
- Apply least privilege to directory and Azure RBAC roles; review Global Admins.
- Enable sign-in risk policies and alert on illicit consent and anomalous token use.

## Related

- [Active Directory Basics](Active%20Directory%20Basics.md), [Azure](../Cloud/Azure.md)
- [CARTP cert](../Certifications/CARTP.md), [OAuth 2.0](../Terminology/OAuth%202.0.md), [SAML](../Terminology/SAML.md)
