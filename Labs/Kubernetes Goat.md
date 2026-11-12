# Kubernetes Goat

Kubernetes Goat is a free, intentionally vulnerable **Kubernetes** cluster for learning cloud-native and container attacks hands-on. It bundles a set of scenarios (vulnerable apps, misconfigurations, and escape paths) with guided walkthroughs, so you can practice the attacks described in [Kubernetes Security](../Containers/Kubernetes%20Security.md) safely.

## Run it

```bash
git clone https://github.com/madhuakula/kubernetes-goat
cd kubernetes-goat
# Point kubectl at a throwaway cluster (kind/minikube/cloud), then:
bash setup-kubernetes-goat.sh
bash access-kubernetes-goat.sh    # port-forward the scenarios locally
```

Use a disposable local cluster (kind or minikube) so nothing leaks into real infrastructure.

## What you practice

- Container escape to the host node
- Exposed Docker/Kubelet APIs and dashboards
- Secrets stored insecurely in env vars, config maps, and images
- Over-permissive RBAC and service-account token abuse
- SSRF to the cloud metadata endpoint from a pod
- Scanning images and manifests (pairs with [Trivy](../Cloud/Trivy.md))

## How to approach it

1. Deploy the cluster and read the scenario list.
2. Treat a single compromised pod as your foothold - what can its service account do?
3. Hunt secrets, then escalate via RBAC or a node escape.
4. Compare your findings against the [Kubernetes Security](../Containers/Kubernetes%20Security.md) hardening guide.

## Resources

- [Kubernetes Goat](https://github.com/madhuakula/kubernetes-goat)
- [Kubernetes Security](../Containers/Kubernetes%20Security.md), [Docker Security](../Containers/Docker%20Security.md)
