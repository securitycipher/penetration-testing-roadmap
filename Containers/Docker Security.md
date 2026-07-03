# Docker Security

Containers are not VMs. Misconfigs let attackers escape to the host or access other containers.

## Common misconfigurations

- Running containers as **root**
- Mounting `/var/run/docker.sock` into a container
- `--privileged` flag enabled
- Secrets in environment variables or Dockerfile
- Outdated base images with known CVEs

## Testing checklist

```bash
# Scan image for CVEs
trivy image myapp:latest

# Check if container is privileged (inside container)
cat /proc/self/status | grep CapEff

# Docker socket exposed?
ls -la /var/run/docker.sock
```

## Escape vectors

- Privileged container + mounted docker.sock = host root
- CVE-specific escapes (check kernel and runc versions)
- Writable host paths mounted into container

## Tools

| Tool | Use |
|------|-----|
| [Trivy](https://github.com/aquasecurity/trivy) | Image and filesystem CVE scan |
| [Docker Bench](https://github.com/docker/docker-bench-security) | Host configuration audit |
| [cdk](https://github.com/cdk-team/CDK) | Container penetration toolkit |

## Practice

- [TryHackMe - Container security rooms](https://tryhackme.com)
- [Kubernetes Goat](https://madhuakula.com/kubernetes-goat/)

## Related

- [Kubernetes Security](Kubernetes%20Security.md)
