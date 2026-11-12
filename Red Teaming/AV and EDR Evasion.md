# AV and EDR Evasion

Antivirus (AV) and Endpoint Detection and Response (EDR) are the main obstacles between a red teamer and a working foothold. AV is mostly signature and heuristic based; EDR goes further, hooking API calls, watching behavior, and streaming telemetry to a SOC. Evasion is about getting code to run **and stay quiet**, not just dodging one scanner. This is the heart of [OSEP](../Certifications/OSEP.md) and [CRTO](../Certifications/CRTO.md).

## How modern EDR sees you

- **User-mode API hooking** - EDR injects into processes and watches calls like `VirtualAlloc`, `CreateRemoteThread`.
- **AMSI** - scans scripts/PowerShell in memory before execution.
- **ETW (Event Tracing for Windows)** - rich telemetry on process, .NET, and more.
- **Kernel callbacks** - process/thread/image-load notifications.
- **Behavioral detection** - suspicious parent/child chains, LSASS access, injection patterns.

## Evasion techniques (concepts)

- **Obfuscation / packing** - break static signatures (but behavior still shows).
- **AMSI bypass** - patch or evade the AMSI scan buffer for script execution.
- **ETW patching** - blind the .NET/ETW telemetry channel.
- **Unhooking** - restore clean copies of `ntdll` to remove EDR's user-mode hooks.
- **Direct / indirect syscalls** - call the kernel directly, skipping hooked API functions.
- **Process injection variants** - APC, process hollowing, module stomping, callback-based execution.
- **BYOVD** - bring a vulnerable signed driver to disable EDR from the kernel (noisy, advanced).
- **Living off the land (LOLBAS)** - use trusted Windows binaries so nothing new drops to disk.
- **Payload staging** - keep as much as possible in memory; avoid touching disk.

## The right mindset

Evasion is a moving target - no bypass lasts forever. Focus on *why* a detection fires: static signature, API hook, or behavior. Defeat the specific layer, and test against the specific EDR in a lab. Reducing footprint (fewer actions, trusted tools, memory-only) often beats a clever bypass.

## Test it safely

- Build a detection lab with the target EDR plus a [SIEM](../Terminology/SIEM.md).
- Validate payloads against tools like a local AV first, but remember a clean VirusTotal score is not EDR-safe.
- Correlate what you do with what the blue team sees (purple teaming).

## Defensive side

- Keep EDR tamper protection on; alert on unhooking, driver loads, and LSASS access.
- Enable AMSI, script block logging, and attack-surface-reduction rules.
- Watch for LOLBAS abuse and anomalous process lineage.

## Related

- [Command and Control (C2)](Command%20and%20Control%20(C2).md), [PowerShell for Pentesters](../Scripting/PowerShell%20for%20Pentesters.md)
- [MITRE ATT&CK](MITRE%20ATT%26CK.md) - Defense Evasion is tactic TA0005
