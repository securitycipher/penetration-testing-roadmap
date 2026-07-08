# Android
Android is a mobile operating system developed by Google. It is the most widely used operating system for mobile devices like smartphones and tablets. Here are some key points about Android that can help you understand it better:

## Open Source

Android is an open-source operating system, which means that its source code is freely available to the public. This openness allows developers to customize and modify the code to create their own versions of Android.
## Google's Involvement

Android was initially developed by Android Inc., which was later acquired by Google in 2005. Since then, Google has been the primary developer and maintainer of the Android operating system.
##User Interface

Android provides a user-friendly interface that includes a home screen with app icons and a navigation system. Users can customize their home screen, add widgets, and organize their apps in folders.
## App Ecosystem

Android has a vast ecosystem of applications available through the Google Play Store. Users can download and install apps to enhance the functionality of their devices, ranging from productivity tools to entertainment apps.
## Customization

One of the notable features of Android is its high level of customization. Users can personalize their devices by changing wallpapers, themes, and even the entire look and feel of the user interface. Additionally, Android supports a variety of widgets that provide quick access to information and functions without opening the full app.
## Multitasking

Android allows multitasking, enabling users to run multiple apps simultaneously. This feature is particularly useful for switching between different tasks without having to close and reopen apps constantly.
## Notifications

Android's notification system is designed to keep users informed about updates, messages, and other important events. Notifications appear on the status bar and can be expanded for more details or dismissed with a swipe.
## Integration with Google Services

Android is tightly integrated with various Google services such as Gmail, Google Maps, Google Drive, and more. This integration provides a seamless experience for users who use Google's ecosystem of products.
## Security

Android places a strong emphasis on security. It includes features like app sandboxing, secure boot process, and regular security updates. Google Play Protect is a built-in security feature that scans apps for malware before and after installation.
## Device Variety

Android is used by a wide range of device manufacturers, resulting in a diverse array of smartphones and tablets. This diversity allows users to choose devices that suit their preferences and budget.

In summary, Android is a versatile and customizable operating system designed for mobile devices, offering a wide range of features, a vast app ecosystem, and compatibility with various hardware from different manufacturers. Its open-source nature and integration with Google services contribute to its popularity among users worldwide.

---

## Android from a security-testing lens

Android's security rests on **app sandboxing** (each app runs as its own Linux UID), **permissions**, and **SELinux**. Pentesting focuses on the app layer and how it stores/transmits data.

### Quick device interaction (ADB)

```bash
# Connect and inspect
adb devices
adb shell getprop ro.build.version.release
adb shell pm list packages | grep target      # find the app package

# Pull an installed APK for analysis
adb shell pm path com.target.app
adb pull /data/app/.../base.apk

# App data (needs root) and logs
adb shell run-as com.target.app ls -la /data/data/com.target.app
adb logcat | grep -i target
```

### What to look for

- **Insecure data storage** — plaintext creds/tokens in SharedPreferences, SQLite, or files.
- **Exported components** — activities/services/receivers callable by other apps.
- **Weak crypto / hardcoded keys** — found by decompiling the APK.
- **Cleartext / no cert pinning** — traffic interceptable via Burp.

See the full workflow in [Android Security Testing](../Mobile/Android%20Security%20Testing.md).

## Related

- [Android Security Testing](../Mobile/Android%20Security%20Testing.md)
- [Operating System Hardening](Operating%20System%20Hardening.md)
