# Frida

Frida is a dynamic instrumentation toolkit. It injects a JavaScript engine into a running process so you can **hook functions, read and modify arguments and return values, and dump memory at runtime** - without the source code. It is the backbone of mobile app testing (see [Android](../Mobile/Android%20Security%20Testing.md) and [iOS](../Mobile/iOS%20Security%20Testing.md)) and useful for desktop apps too.

## Install

```bash
pip install frida-tools          # CLI (frida, frida-ps, frida-trace)
# On the target device you need frida-server (rooted Android / jailbroken iOS)
```

## Common commands

```bash
frida-ps -U                      # list processes on a USB device
frida-ps -Uai                    # list installed apps (Android)
frida -U -f com.target.app -l hook.js --no-pause   # spawn app with a script
frida-trace -U -i "open" com.target.app            # auto-trace a function
```

## What you use it for

- **SSL pinning bypass** so you can see app traffic in [Burp Suite](Burp%20Suite.md)
- **Root / jailbreak detection bypass**
- Dumping secrets, keys, and decrypted data from memory
- Changing function behavior (force a check to return `true`)
- Calling internal functions directly

## Example: hook a function

```javascript
// Hook a Java method on Android and log + change its result
Java.perform(function () {
  var Check = Java.use("com.target.app.security.RootCheck");
  Check.isRooted.implementation = function () {
    console.log("[*] isRooted() called - returning false");
    return false;
  };
});
```

## objection (Frida made easy)

[objection](https://github.com/sensepost/objection) wraps Frida with ready-made commands - no scripting needed for common tasks:

```bash
objection -g com.target.app explore
# then inside:
android sslpinning disable
android root disable
ios keychain dump
```

## Tips

- Match the frida-server version to your client version.
- For non-rooted Android, you can patch the APK to embed a Frida gadget.
- Keep a library of reusable hook scripts (pinning, root, crypto).

## Resources

- [Frida docs](https://frida.re/docs/home/)
- [Frida CodeShare](https://codeshare.frida.re/) - community scripts
