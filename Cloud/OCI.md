# What is Oracle Cloud Infrastructure (OCI)?
Oracle Cloud Infrastructure (OCI) is the cloud computing service offered by Oracle Corporation. It provides a comprehensive suite of cloud services, including computing, storage, networking, databases, and more. OCI is designed to deliver high-performance, scalability, and security for a wide range of applications and workloads.

## Key Concepts

- Regions and Availability Domains:

  - OCI is organized into regions, each comprising multiple Availability Domains (ADs). Availability Domains are isolated data centers within a region, offering fault tolerance and high availability.
- Compute Services:

  - Compute Instances: OCI offers various compute instances similar to VMs on other cloud platforms. You can choose from different shapes and sizes to match your application requirements.
  - Oracle Functions: A serverless computing service for running code without managing infrastructure.
- Storage Services:

  - Object Storage: Provides scalable and durable object storage for storing and retrieving data, similar to other cloud providers' object storage services.
  - Block Volumes: Offers block storage that can be attached to compute instances.
- Database Services:

  - Oracle Autonomous Database: A fully managed database service that automatically handles database administration tasks like patching, backups, and tuning.
  - MySQL Database Service: A fully managed MySQL database service for developers and businesses.
- Networking:

  - Virtual Cloud Network (VCN): Allows you to create and manage your own virtual network within OCI, similar to VPC in other cloud platforms.
  - Load Balancing: Distributes incoming traffic across multiple instances for better availability and reliability.
- Security:

  - Identity and Access Management (IAM): Manages access and permissions to OCI resources.
  - Security Zones: A security boundary within which you can isolate your resources and control traffic flow.
- Management Tools:

  - OCI Console: A web-based interface for managing and monitoring OCI resources.
  - OCI CLI: Command-line tools for interacting with OCI services.

## Advantages of OCI

- High-Performance Computing: OCI is designed for high-performance computing workloads, making it suitable for resource-intensive applications.
- Enterprise-Grade Databases: Offers robust database services, leveraging Oracle's expertise in database technologies.
- Integrated Cloud and On-Premises Solutions: Provides solutions for hybrid cloud scenarios, allowing integration with on-premises data centers.

## Getting Started

- Sign up for an OCI account on the Oracle Cloud website.
- Explore the free tier to try out various OCI services.
- OCI provides extensive documentation and tutorials to help you understand and use their services.

Just like with other cloud platforms, starting with specific services based on your needs and gradually expanding your knowledge will help you make the most of Oracle Cloud Infrastructure.

---

## Security testing perspective

OCI uses a **compartment**-based model and **instance principals** for workload identity. The attack patterns mirror AWS/GCP: leaked keys, over-permissive policies, public buckets, and metadata credential theft.

### Setup and enumeration

```bash
# Install and configure the OCI CLI
bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
oci setup config

# Identity / who am I
oci iam region list
oci iam compartment list --all
oci iam user list

# Object Storage buckets (public buckets are a common finding)
oci os ns get
oci os bucket list -c <compartment-ocid>
oci os object list -bn <bucket>

# Compute instances
oci compute instance list -c <compartment-ocid>
```

### Metadata credential theft (SSRF on an OCI instance)

```bash
# Instance principal / identity metadata
curl -s http://169.254.169.254/opc/v2/instance/ -H "Authorization: Bearer Oracle"
curl -s http://169.254.169.254/opc/v2/identity/cert.pem -H "Authorization: Bearer Oracle"
```

### Automated tooling

```bash
prowler oci          # OCI checks supported
scout suite oci      # alpha OCI support
```

## Related

- [Top Cloud Security Risks](Top%20Cloud%20Security%20Risks.md)
- [AWS](AWS.md) · [GCP](GCP.md)
