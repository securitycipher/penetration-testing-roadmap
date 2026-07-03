# Android Security Testing

Mobile pentesting combines static analysis (APK decompilation), dynamic instrumentation, and API testing of the backend the app talks to.

## Setup

- **Emulator** - Android Studio AVD or Genymotion with root
- **Proxy** - Burp with user-installed CA cert on device
- **Tools** - jadx, apktool, Frida, MobSF

## What to test

- Insecure local storage (SharedPreferences, SQLite)
- Hardcoded secrets in APK
- Certificate pinning bypass
- Exported components (activities, broadcast receivers)
- Deep link hijacking
- API calls the app makes (replay in Burp)

## Quick workflow

```bash
# Decompile APK
jadx -d output/ app.apk

# Repackage after modifying smali (advanced)
apktool d app.apk

# Frida SSL pinning bypass (example)
frida -U -f com.target.app -l ssl-bypass.js
```

## Tools

| Tool | Use |
|------|-----|
| [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF) | Automated static/dynamic scan |
| [Frida](https://frida.re/) | Runtime instrumentation |
| [objection](https://github.com/sensepost/objection) | Frida-powered mobile toolkit |
| [drozer](https://github.com/WithSecureLabs/drozer) | Android IPC testing |

## Certs

- [eMAPT](https://elearnsecurity.com/product/emapt-certification/) - mobile-specific
- [GMOB](https://www.infosecinstitute.com/skills/learning-paths/certified-mobile-security-professional-gmob/) - mobile security professional

## Related

- [iOS Security Testing](iOS%20Security%20Testing.md)
