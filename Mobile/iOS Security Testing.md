# iOS Security Testing

iOS apps run sandboxed and Apple locks the platform down hard, but storage, keychain, networking, and logic bugs are still common - and the backend API behind the app is the same target as any web test. The OWASP **MASVS/MASTG** frames the methodology just like it does for Android.

## Setup (harder than Android)

- A **Mac** with Xcode
- A **jailbroken device** or **Corellium** (virtual iOS) for full dynamic analysis - this is the main hurdle on iOS
- [Burp Suite](../Tools/Burp%20Suite.md) with the CA profile installed and trusted on the device
- Tools: [Frida](../Tools/Frida.md), objection, class-dump, [Ghidra](../Tools/Ghidra.md)/Hopper for the Mach-O binary

## App structure

- `.ipa` is a zip; the app bundle holds the Mach-O binary, `Info.plist`, and resources
- App Store binaries are **encrypted (FairPlay)** - you decrypt on a jailbroken device (frida-ios-dump) before static analysis
- Data lives in the app's sandboxed container (Documents, Library, tmp) and the **Keychain**

## What to test (OWASP Mobile Top 10 themes)

- **Keychain** - items stored with weak accessibility (available without passcode/when locked)
- **Insecure local storage** - plists, NSUserDefaults, Core Data, cached files
- **App Transport Security (ATS)** bypass and cleartext/weak TLS
- **Certificate pinning** bypass
- **Jailbreak detection** quality and bypass
- **URL scheme / universal link hijacking** and improper handling
- **Sensitive data in logs, snapshots, or the pasteboard**
- **Backend API** - test as a normal [web/API target](../API%20Security/OWASP%20API%20Top%2010.md)

## Workflow

```bash
# Decrypt + dump an App Store app (jailbroken device)
frida-ios-dump -l                       # list apps
frida-ios-dump com.target.app           # pull a decrypted .ipa

# Dynamic testing with objection (Frida under the hood)
objection -g com.target.app explore
# then inside:
ios sslpinning disable
ios jailbreak disable
ios keychain dump
ios nsuserdefaults get
```

## Frida hook example

```javascript
// Force a jailbreak-detection method to return false
var cls = ObjC.classes.JailbreakDetector;   // app-specific class name
Interceptor.attach(cls['- isJailbroken'].implementation, {
  onLeave: function (ret) { ret.replace(0x0); }
});
```

## Tools

- [objection](https://github.com/sensepost/objection) and [Frida](../Tools/Frida.md) - runtime manipulation
- [MobSF](../Tools/MobSF.md) - automated static analysis of the IPA
- class-dump / [Ghidra](../Tools/Ghidra.md) - inspect the binary
- [ipsw](https://github.com/blacktop/ipsw) - firmware/binary analysis

## Practice and certs

- [OWASP MASVS / MASTG](https://mas.owasp.org/) and its crackmes
- [eMAPT](../Certifications/eMAPT.md) covers mobile broadly

## Related

- [Android Security Testing](Android%20Security%20Testing.md)
