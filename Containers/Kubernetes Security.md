# Kubernetes Security

Kubernetes (K8s) orchestrates containers at scale. RBAC mistakes and exposed APIs are the fastest paths to cluster compromise.

## Attack surface

- **kube-apiserver** exposed to internet
- Overly permissive **RBAC** (any pod can list secrets cluster-wide)
- **Dashboard** without auth or with weak creds
- **etcd** backups accessible
- Secrets mounted as env vars in pod specs

## Enumeration

```bash
# From inside a compromised pod
curl -k https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORT/api/v1/namespaces
kubectl auth can-i --list  # if kubectl available

# External scan
nmap -p 6443,10250 target.com
```

## Tools

| Tool | Use |
|------|-----|
| [kube-hunter](https://github.com/aquasecurity/kube-hunter) | Passive/active K8s pentest |
| [kube-bench](https://github.com/aquasecurity/kube-bench) | CIS benchmark checks |
| [kubectl](https://kubernetes.io/docs/reference/kubectl/) | Cluster interaction |
| [peirates](https://github.com/inguardians/peirates) | K8s privilege escalation |

## Practice

- [Kubernetes Goat](https://madhuakula.com/kubernetes-goat/)
- [HackTheBox - Kubernetes boxes](https://www.hackthebox.com)

## Cloud overlap

- [AWS EKS checklist](https://securitycipher.com/aws-cloud-security-checklist/)
- [Prowler](Cloud/Prowler.md) - cloud + K8s misconfig scans
