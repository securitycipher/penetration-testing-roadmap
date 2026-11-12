# MobSF (Mobile Security Framework)

MobSF is an all-in-one, open-source tool for mobile app security testing. Upload an APK (Android) or IPA (iOS) and it runs automated **static analysis**, and with a connected device/emulator, **dynamic analysis** too. It is the fastest way to get a baseline on a mobile app before you dig in manually with [Frida](Frida.md).

## Run it (Docker is easiest)

```bash
docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest
# then open http://localhost:8000 and upload the APK/IPA
```

## Static analysis - what it flags

- Hardcoded secrets, API keys, and URLs
- Dangerous permissions and exported components (activities, receivers, providers)
- Insecure storage, weak crypto, and cleartext traffic settings
- Certificate/signing info and the `AndroidManifest.xml` breakdown
- Maps findings to the **OWASP MASVS / Mobile Top 10**
- Decompiled source browsing and a risk score

## Dynamic analysis

- Runs the app on a connected Android emulator/device (Frida-backed)
- Captures network traffic, runtime API calls, and generated files
- Helps with SSL pinning / runtime checks before you script hooks yourself

## Where it fits in a mobile test

1. **MobSF first** for a quick automated baseline and attack surface map.
2. Use its findings (exported components, secrets, endpoints) to focus manual testing.
3. Go deep with [Frida](Frida.md)/objection for pinning bypass and runtime manipulation.
4. Test the backend API like any web/API target (see [OWASP API Top 10](../API%20Security/OWASP%20API%20Top%2010.md)).

## Tips

- Great for a report-ready first pass, but automated tools miss business logic - always follow up manually.
- The REST API lets you wire MobSF into CI for every build.

## Resources

- [MobSF on GitHub](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- [OWASP MASVS / MASTG](https://mas.owasp.org/)
- [Android Security Testing](../Mobile/Android%20Security%20Testing.md), [iOS Security Testing](../Mobile/iOS%20Security%20Testing.md)
