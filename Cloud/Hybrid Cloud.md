# What is a Hybrid Cloud?

A Hybrid Cloud is a computing environment that combines elements of both Public and Private Clouds. It allows data and applications to be shared between them, providing greater flexibility and more deployment options. Essentially, it's like having a mix of a private, exclusive space and a shared, public space that work together seamlessly.

## Key Characteristics of a Hybrid Cloud:

- Integration of Public and Private Clouds: A Hybrid Cloud integrates infrastructure, services, and applications from both Public and Private Clouds.

- Data and Application Portability: Data and applications can move seamlessly between the Public and Private Cloud components of the hybrid environment.

- Orchestration: Orchestration tools and technologies are used to manage and coordinate workloads across the different cloud environments.

- Flexibility: Organizations can choose where to run specific workloads based on factors like cost, security, and performance requirements.

## How a Hybrid Cloud Works:

Imagine you have some data and applications that require the high security and customization of a Private Cloud, while others are more flexible and cost-effective to run in a Public Cloud. With a Hybrid Cloud:

- Sensitive Data on Private Cloud: You can keep sensitive data on the Private Cloud part, ensuring it's well-protected and complies with any industry regulations.

- Scalable Applications on Public Cloud: Applications that require a lot of computing power or need to scale rapidly can run on the Public Cloud, taking advantage of its flexibility and scalability.

- Seamless Communication: The Private and Public Cloud components can communicate and share data, creating a cohesive and integrated computing environment.

## Advantages of Using a Hybrid Cloud:

- Flexibility: Organizations have the flexibility to choose the most suitable cloud environment for each workload or application.

- Cost Optimization: It allows for cost optimization by leveraging the cost-effective resources of the Public Cloud while keeping critical or sensitive workloads on the Private Cloud.

- Scalability: Hybrid Clouds provide the ability to scale resources up or down based on fluctuating demands, offering both the scalability of the Public Cloud and the control of the Private Cloud.

- Disaster Recovery: The redundancy provided by a Hybrid Cloud setup can enhance disaster recovery capabilities. Critical data can be backed up in the Public Cloud, while core operations remain on the Private Cloud.

## Common Use Cases:

- Data Security and Compliance: Industries with strict data security and compliance requirements, such as healthcare and finance, can keep sensitive data on a Private Cloud while utilizing the scalability of the Public Cloud for other services.

- Variable Workloads: Applications with variable workloads that may require more resources at certain times can benefit from the scalability of the Public Cloud, with a baseline running on a Private Cloud.

- Development and Testing: Organizations can use the Public Cloud for development and testing purposes while keeping production environments on a Private Cloud.

In summary, a Hybrid Cloud is like having the best of both worlds—combining the control and security of a Private Cloud with the flexibility and scalability of a Public Cloud. It's a strategic approach that allows organizations to optimize their IT infrastructure based on specific needs and requirements.

---

## Security testing perspective

The **connective tissue** between on-prem and public cloud is the juiciest target: the links, sync identities, and trust relationships that let one environment reach the other.

### High-value attack paths

- **VPN / Direct Connect / ExpressRoute** — pivot from a compromised cloud host into the corporate network (and vice versa).
- **Identity federation** — **Azure AD Connect / ADFS** syncs on-prem AD to the cloud. Compromise on-prem AD → cloud, or abuse federation to forge cloud tokens (Golden SAML).
- **Shared service accounts** — creds valid in both environments; steal once, use everywhere.
- **Inconsistent controls** — the on-prem side often lacks the cloud side's logging/MFA.

### Commands

```bash
# On a compromised cloud VM, look for routes/tunnels back on-prem
ip route; cat /etc/hosts
nmap -sn 10.0.0.0/8            # can we reach internal RFC1918 ranges?

# Enumerate AD federation / sync servers (from AD)
# AADInternals — hunt for AD Connect and dump sync credentials
Get-AADIntSyncCredentials

# Golden SAML requires the ADFS token-signing cert -> forge cloud tokens
```

### Focus

Treat hybrid as **two pentests plus the bridge**: assess public ([AWS](AWS.md)/[Azure](Azure.md)/[GCP](GCP.md)), assess private ([Private Cloud](Private%20Cloud.md)), then prove lateral movement across the link.

## Related

- [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md)
- [Azure](Azure.md) · [Top Cloud Security Risks](Top%20Cloud%20Security%20Risks.md)
