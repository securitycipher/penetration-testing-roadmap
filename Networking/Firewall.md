# Firewall

A firewall decides which packets are allowed between networks. On an internal pentest it is the control that should stop a compromised DMZ host, a guest Wi-Fi client, or a user subnet from reaching the domain controllers.

## What to look for

- **Any-any rules.** A permit from the user VLAN to the whole server range is lateral movement on a platter.
- **Management exposed.** SSH, RDP, WinRM, or the firewall admin UI reachable from a less-trusted zone.
- **Egress wide open.** Outbound 80/443 to anywhere lets a shell phone home. Also check DNS (53) and ICMP.
- **Rule order.** A later deny does nothing if an earlier permit already matched.
- **Stale objects.** Decommissioned hosts left in "allow" groups.

## How to test it

```bash
# Filtered vs closed tells you a filter is in the path
nmap -sS -p 22,80,135,139,445,3389,5985 10.10.10.0/24

# From a host you already own: can you reach the DC subnet?
nmap -Pn -p 88,389,445,3389 10.10.20.10

# Egress: which outbound ports actually leave?
for p in 53 80 443 4444 8080; do
  timeout 3 bash -c "echo >/dev/tcp/1.1.1.1/$p" && echo "open $p"
done
```

Map the result back to the rulebase if the client gives you read access (config export, `show access-list`, or the management UI). A finding is a rule that violates the segmentation they described in the rules of engagement, not "a firewall exists."

## Related

- [IDS](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Networking/IDS.md) · [IPS](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Networking/IPS.md)
- [DMZ](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Terminology/DMZ.md) · [VPN](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/Networking/VPN.md)
