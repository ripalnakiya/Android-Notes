# Fundamentals
<show-structure depth="2"/>

Android is an **open-source operating system** primarily designed for mobile devices such as smartphones and tablets.

It is based on the **Linux kernel** and developed by a consortium of developers known as the Open Handset Alliance, **led by Google**.

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
{style="note"}

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

## Gradle

Android Studio IDE uses **Gradle** as its build system.

Gradle is a powerful and flexible build automation tool
used in software development, particularly in the Java and Android ecosystems.

It is designed to manage the **build lifecycle of projects**,
_automate_ the **compilation and packaging processes**,
and **handle dependencies** efficiently.

Gradle is the tool working behind the scenes to **compile and package your app**.

### `compileSdk`

The **`compileSdk`** determines which Android and Java APIs are available when compiling your source code.
To use the latest Android features, use the latest Android SDK when compiling.

Some Android platform APIs might not be available in older API levels. 
You can conditionally guard use of newer features or 
use AndroidX compatibility libraries to use newer features with lower Android API levels.

### `targetSdk`

The **`targetSdk`** serves two purposes:
1. It sets runtime behaviour of your application.
2. It attests which version of Android you've tested against.

If you run on a device that's using a higher version of Android than your `targetSdk`, 
Android runs your app in a compatibility mode that behaves similarly to the lower version indicated in your `targetSdk`. 

> This is more like a certification or sign off you are giving the Android OS as a hint to how it should handle your app in terms of OS features.
{style="note"}

For example, when API 23 introduced the runtime permissions model, not all apps were ready to immediately adopt it. 
By setting `targetSdk` to 22, those apps could run on API 23 devices without using runtime permissions, 
and could use features included in the latest `compileSdk` version.

The value of `targetSdk` is not connected to the value of `compileSdk`. 
For example, you can have a value of `targetSdk` that is higher, the same, or lower than `compileSdk`.

> Note: The values of compileSdk and targetSdk don't need to be the same. 
> Keep the following basic principles in mind:
> 
> `compileSdk` gives you access to new APIs
> 
> `targetSdk` sets the runtime behavior of your app
{style="note"}

### `minSdk`

The `minSdk` specifies the lowest version of Android that you want your app to support. 
Setting `minSdk` restricts which devices can install your app.
