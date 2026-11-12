# Android Security Testing

Android pentesting has three parts: **static analysis** of the APK, **dynamic analysis** of the running app, and testing the **backend API** the app talks to (often where the real bugs are). The OWASP **MASVS/MASTG** is the standard that frames all of it.

## Setup

- **Emulator** - Android Studio AVD or Genymotion, rooted, with a writable system for CA certs
- **Proxy** - [Burp Suite](../Tools/Burp%20Suite.md) with its CA installed at the system level (user certs are ignored by apps targeting newer API levels)
- **Tools** - jadx, apktool, [Frida](../Tools/Frida.md), [MobSF](../Tools/MobSF.md), objection, adb

## APK structure (know what you are looking at)

- `AndroidManifest.xml` - components, permissions, exported flags, deep links
- `classes.dex` - the compiled app code (decompile with jadx)
- `resources.arsc` / `res/` - resources and sometimes secrets
- `lib/` - native `.so` libraries (reverse with [Ghidra](../Tools/Ghidra.md))
- `assets/` - bundled files, sometimes hardcoded keys/configs

## What to test (OWASP Mobile Top 10 themes)

- **Insecure data storage** - SharedPreferences, SQLite, files, external storage
- **Hardcoded secrets** - API keys, tokens, endpoints in the APK
- **Exported components** - activities, services, broadcast receivers, content providers reachable by other apps
- **Deep link / intent hijacking** and improper intent handling
- **Certificate pinning** and whether it can be bypassed
- **Insecure communication** - cleartext traffic, weak TLS config
- **Weak crypto** and insecure random
- **Root detection** quality (and bypass)
- **Backend API** - test it like any [web/API target](../API%20Security/OWASP%20API%20Top%2010.md)

## Workflow

```bash
# 1. Pull and decompile
adb shell pm path com.target.app       # find the APK path
adb pull <path> app.apk
jadx -d output/ app.apk                 # readable Java
apktool d app.apk                       # smali + resources (for repackaging)

# 2. Automated baseline
# upload app.apk to MobSF for a static + dynamic pass

# 3. Dynamic: intercept + bypass pinning
frida -U -f com.target.app -l ssl-bypass.js --no-pause
objection -g com.target.app explore     # then: android sslpinning disable

# 4. Inspect storage after using the app
adb shell run-as com.target.app ls -la /data/data/com.target.app/
```

## Tools

| Tool | Use |
|------|-----|
| [MobSF](../Tools/MobSF.md) | Automated static/dynamic scan, MASVS mapping |
| [Frida](../Tools/Frida.md) | Runtime hooking, pinning/root bypass |
| [objection](https://github.com/sensepost/objection) | Frida-powered mobile toolkit, no scripting |
| jadx / apktool | Decompile and repackage |
| [drozer](https://github.com/WithSecureLabs/drozer) | Android IPC / exported component testing |

## Certs

- [eMAPT](../Certifications/eMAPT.md) - practical mobile cert
- GMOB - GIAC mobile security

## Resources

- [OWASP MASVS / MASTG](https://mas.owasp.org/)
- [iOS Security Testing](iOS%20Security%20Testing.md)
