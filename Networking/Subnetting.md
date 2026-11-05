# Subnetting

Subnetting splits one network into smaller ones. For a pentest, the question is not the math quiz. It is whether those boundaries actually stop you moving from a low-trust host to a high-value one.

## What you need in the field

| CIDR | Hosts (usable) | Typical use |
|------|----------------|-------------|
| /8 | 16 million | Far too flat. Treat as a finding if user and server traffic share it. |
| /16 | 65,534 | Common "one big internal network" |
| /24 | 254 | A single VLAN |
| /27-/28 | 30 / 14 | Small server or management segments |

```bash
# Your address, mask, and gateway
ip addr; ip route
ipconfig /all

# Who else is on this subnet
nmap -sn 10.10.10.0/24

# Routes that leak into other zones
route print
ip route show
```

## What to test

- **Flat network.** Workstations can reach SMB, RDP, and WinRM on servers with no filter in between.
- **DMZ to internal.** A host in the DMZ can open 445/3389/1433 on the internal range. See [DMZ](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Terminology/DMZ.md).
- **Dual-homed jump.** A server with one NIC in the user VLAN and one in the server VLAN bypasses the firewall.
- **VPN pools.** Remote-access clients land in a subnet that has the same access as an office workstation, including admin shares.

Write the finding as the path you actually walked (source subnet, dest subnet, port), not as "the network should be segmented."

## Related

- [Types of Networks](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Networking/Types%20of%20Networks.md) · [Firewall](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Networking/Firewall.md)
- [DMZ](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Terminology/DMZ.md) · [VPN](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Networking/VPN.md)
