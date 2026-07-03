# iOS Security Testing

iOS apps run in a sandboxed environment, but misconfigs in storage, networking, and keychain usage are still common findings.

## Setup

- Mac with Xcode
- Jailbroken device or Corellium for full dynamic analysis
- Burp proxy with iOS CA profile installed
- Tools: Frida, objection, class-dump, Hopper/Ghidra for binary analysis

## What to test

- Keychain items accessible without device passcode
- Sensitive data in plist files or app bundle
- ATS (App Transport Security) bypass
- Jailbreak detection bypass
- URL scheme / universal link hijacking
- Backend API security (same as web/API testing)

## Frida example

```javascript
// Hook insecure URL loading (example pattern)
Java.perform(function() {
  // Use objection for quick checks: objection -g com.target.app explore
});
```

```bash
objection -g com.target.app explore
# run ios sslpinning disable
# run ios keychain dump
```

## Tools

- [objection](https://github.com/sensepost/objection)
- [Frida](https://frida.re/)
- [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- [ipsw](https://github.com/blacktop/ipsw) - firmware analysis

## Practice

- [OWASP MSTG](https://owasp.org/www-project-mobile-security-testing-guide/)
- [HackTheBox mobile challenges](https://www.hackthebox.com)

## Related

- [Android Security Testing](Android%20Security%20Testing.md)
