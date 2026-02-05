# Fundamentals

Android is an **open-source operating system** primarily designed for mobile devices such as smartphones and tablets.

It is based on the **Linux kernel** and developed by a consortium of developers known as the Open Handset Alliance, **led by Google**.

## Gradle

Android Studio IDE uses **Gradle** as its build system.

Gradle is a powerful and flexible build automation tool 
used in software development, particularly in the Java and Android ecosystems.

It is designed to manage the **build lifecycle of projects**, 
_automate_ the **compilation and packaging processes**, 
and **handle dependencies** efficiently.

Gradle is the tool working behind the scenes to **compile and package your app**.

## SDK

Software Development Kit is a pile of tools, libraries, and APIs 
that let you build, run, test, and debug Android apps.

### What it essentially contains

1. **Android APIs:** These are the classes you actually code against.
   - Activity, Service, BroadcastReceiver, ContentProvider
   - UI stuff like View, RecyclerView, ConstraintLayout
   - System services: camera, location, sensors, storage, network, etc.

2. **SDK Tools:** Command-line tools.
   - `adb` (Android Debug Bridge) for installing apps, logs, debugging
   - `sdkmanager` to download SDK components
   - `avdmanager` for emulators

3. **Platform Tools:** Low-level tools tied closely to the OS.
   - adb, fastboot
   - Used for device communication and system-level operations

4. **Build Tools:** Used during compilation and packaging.
   - aapt / aapt2 (resource packaging)
   - d8 / r8 (bytecode → dex, shrinking, obfuscation)
   - zipalign, apksigner

You rarely touch these directly because Gradle automates this.

5. **Android Platforms**
   - Example: Android 13 (API 33), Android 14 (API 34)
   - Each platform = specific APIs + system behavior

6. **Emulator & System Images:** Virtual Android devices.
   - Different API levels
   - Different hardware profiles

### What Android SDK is NOT

Not Android Studio (that’s the IDE)

Not Gradle (that’s the build system)

Not the Android OS itself

> **Android SDK** = everything you need to write code 
> that can talk to the Android OS in a supported way.

Android SDK is a **toolbox** that **helps you build Android apps** 
which use the Android OS framework components 
like Activity, Service, BroadcastReceiver, etc.

### Why SDK Exists

- Without the SDK:
  - You wouldn’t know which methods exist
  - You couldn’t compile your app
  - You’d be guessing system behavior and breaking apps across versions

**The SDK is basically a contract:**
“If you call this method, the OS promises to do this.”

- One-line mental model:
  - SDK = tools + API contracts
  - OS = actual implementation + execution
