# ROADtools

ROADtools is an open-source framework for exploring and attacking **Entra ID (Azure AD)**. Its core, ROADrecon, authenticates to a tenant, pulls the entire directory via the Azure AD Graph, stores it locally, and gives you a web UI plus a queryable database. It is the go-to enumeration tool for cloud identity engagements (see [Entra ID Attacks](../Active%20Directory/Entra%20ID%20Attacks.md) and the [CARTP](../Certifications/CARTP.md) cert).

## Install

```bash
pipx install roadrecon
```

## Workflow

```bash
# Authenticate (username/password, device code, or an existing token)
roadrecon auth -u user@tenant.onmicrosoft.com -p 'Password1'

# Pull the whole directory into roadrecon.db
roadrecon gather

# Launch the local web UI to browse it
roadrecon gui
```

## What you get

- Full inventory: users, groups, **roles**, applications, **service principals**, devices
- Who holds privileged directory roles (Global Admin, etc.)
- App registrations and service principal **credentials/permissions** (prime escalation targets)
- OAuth2 permission grants and conditional access policies
- A local DB you can query for attack paths

## How it fits with other tools

- **ROADtools** - deep Entra ID enumeration and the data layer
- **AzureHound** - exports Entra/Azure data into [BloodHound](../Active%20Directory/BloodHound.md) to visualize attack paths
- **AADInternals** - hybrid and on-prem-to-cloud attacks (PHS, PTA, Golden SAML)
- **[Pacu](Pacu.md)** - the AWS equivalent for resource exploitation

## Mitigation (for the report)

- Least privilege on directory roles and app/service-principal permissions.
- Audit app registrations and consent grants; rotate SP credentials.
- Enforce Conditional Access and phishing-resistant MFA.
- Monitor Graph enumeration and anomalous token use.

## Resources

- [ROADtools on GitHub](https://github.com/dirkjanm/ROADtools)
- [Entra ID Attacks](../Active%20Directory/Entra%20ID%20Attacks.md), [Azure](Azure.md)
