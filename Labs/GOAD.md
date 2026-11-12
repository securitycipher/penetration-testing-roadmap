# GOAD (Game of Active Directory)

GOAD is a free, intentionally vulnerable **Active Directory** lab you deploy yourself. It builds a multi-domain, multi-forest Windows environment full of realistic misconfigurations, so you can practice the full internal-pentest kill chain at home without paying for lab time. It is the best free way to train for [CRTP](../Certifications/CRTP.md), [CPTS](../Certifications/CPTS.md), and the AD portion of [OSCP](../Certifications/OSCP.md).

## What you get

- Multiple Windows Server DCs across linked domains and a forest trust
- Deliberate misconfigurations: Kerberoasting, AS-REP roasting, delegation abuse, ACL paths, [AD CS (ESC)](../Active%20Directory/AD%20CS%20Attacks%20(ESC).md), [NTLM relay](../Active%20Directory/NTLM%20Relay.md) targets
- A documented set of attack paths to work through

## Flavors

- **GOAD** - the full multi-domain/forest lab (heavy; needs lots of RAM)
- **GOAD-Light** - smaller footprint for modest hardware
- **NHA / mini labs** - bite-sized single scenarios

## Deploy options

- Local on **VirtualBox / VMware / Proxmox** via the provided Vagrant + Ansible automation
- Cloud (Azure) if you do not have the hardware

```bash
git clone https://github.com/Orange-Cyberdefense/GOAD
cd GOAD
# follow the provider setup (vagrant up + ansible provisioning)
```

Budget 16 GB+ RAM for the full build; use GOAD-Light otherwise.

## How to practice

1. Enumerate with [BloodHound](../Active%20Directory/BloodHound.md) and PowerView.
2. Work the attack paths: [Kerberoasting](../Active%20Directory/Kerberoasting.md) -> [Pass-the-Hash](../Active%20Directory/Pass-the-Hash.md) -> [DCSync](../Active%20Directory/DCSync.md).
3. Try cross-trust and [AD CS](../Active%20Directory/AD%20CS%20Attacks%20(ESC).md) escalations.
4. Drive it all with [NetExec](../Tools/NetExec.md) and [Impacket](../Tools/Impacket.md).

## Resources

- [GOAD on GitHub](https://github.com/Orange-Cyberdefense/GOAD)
- [Mayfly's GOAD writeups](https://mayfly277.github.io/)
