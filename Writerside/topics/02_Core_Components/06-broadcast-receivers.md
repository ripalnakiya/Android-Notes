# Broadcast Receivers

- A **BroadcastReceiver** is an Android component that:
  - Listens for broadcast messages (called _Intents_)
  - Reacts to system-wide or app-wide events
  - Does not have a UI

A **broadcast** is just an `Intent` sent out to multiple potential listeners.

- Examples of events that **trigger broadcasts**:
  - Device boot completed
  - Battery low / battery okay
  - Network connectivity changed
  - SMS received
  - Screen on / off
  - App-specific events (your own custom broadcasts)

Android apps can send or receive broadcast messages from the Android system 
and other Android apps, similar to the **publish-subscribe pattern**.

## Types of Broadcast Receivers

### 1. Manifest-declared (Static Receivers)

Declared in `AndroidManifest.xml`

```xml
<receiver
    android:name=".AirplaneModeChangeReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.AIRPLANE_MODE"/>
    </intent-filter>
</receiver>
```

Used when:
- You need to receive events even when the app is not running

Important Android reality check:
- Since **Android 8.0 (API 26)**, most implicit broadcasts cannot be registered in the manifest anymore.

Still allowed:
- `BOOT_COMPLETED`
- `SMS_RECEIVED`
- Some system-critical broadcasts

### 2. Context-registered (Dynamic Receivers)

Registered at runtime using code.

**Registering the Receiver:**

```Java
// For connectivity change, use
IntentFilter filter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION);

 // For Airplane Mode
IntentFilter filter = new IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED);

registerReceiver(airplaneModeChangeReceiver, filter);
```

**Broadcast Receiver:**

```Java
public void onReceive(Context context, Intent intent) {

    if(intent.getAction() == ConnectivityManager.CONNECTIVITY_ACTION) {
        Log.d(TAG, "Connectivity Changed");
    }
    if(intent.getAction() == Intent.ACTION_AIRPLANE_MODE_CHANGED){
        // returns 1 when airplane mode is ON, otherwise returns 0
        int isTurnedOn = Settings.Global.getInt(context.getContentResolver(), Settings.Global.AIRPLANE_MODE_ON, 0);

        String status = ((isTurnedOn ==  1) ? "ON" : "OFF");
        Log.d(TAG, "Airplane Mode is " + status);
    }
}
```

**Unregistering the Receiver:**

```Java
unregisterReceiver(airplaneModeChangeReceiver);
```

## Types of Broadcasts

### System Broadcasts

Sent by Android OS.

```Java
Intent.ACTION_BATTERY_LOW
Intent.ACTION_BOOT_COMPLETED
Intent.ACTION_AIRPLANE_MODE_CHANGED
```

### Custom Broadcasts

**APP Sending Custom Broadcast:**

```Java
Intent intent = new Intent();
intent.setAction("com.example.custombroadcast.ACTION_CUSTOM");
sendBroadcast(intent);
```

**APP Receiving Custom Broadcast:**

Registering the receiver:

```Java
CustomBroadcastReceiver customBroadcastReceiver = new CustomBroadcastReceiver();

IntentFilter filter = new IntentFilter();
filter.addAction("com.example.custombroadcast.ACTION_CUSTOM");

registerReceiver(customBroadcastReceiver, filter, Context.RECEIVER_EXPORTED);
```

Broadcast Receiver:

```Java
// Create the `onReceive()` method in the `CustomBroadcastReceiver` class
```

Unregistering the receiver:
```Java
unregisterReceiver(customBroadcastReceiver);
```

## Types of Broadcast delivery

### 1. Normal Broadcast

```Java
sendBroadcast(intent);
```

- Delivered to all matching receivers
- Order is not guaranteed
- Fast, simple, chaotic

### 2. Ordered Broadcast (mostly legacy now)

```Java
sendOrderedBroadcast(intent, null);
```

- Delivered one receiver at a time

Receivers can:
- Modify the Intent
- Abort the broadcast

Used in the past for SMS filtering and similar things.

Modern Android discourages this.

### 3. Local Broadcast (Deprecated)

```Java
LocalBroadcastManager
```

- Only within your app
- More efficient
- No security risk

**Now deprecated because:** we can use LiveData, Flow, SharedViewModel, or RxJava

## `onReceive()` rules

Inside `onReceive()`:

❌ No long-running tasks

❌ No network calls

❌ No sleep

❌ No heavy database operations

Otherwise, app can get killed or get an ANR.

- **Allowed:**
  - Quick checks
  - Logging
  - Starting a Service
  - Scheduling work

## Protected Broadcasts

- A protected broadcast is a system-level broadcast that
third-party apps are NOT allowed to send.
  - You **can receive** it
  - You **cannot send** it
  - Android enforces this at runtime
  - Violate it and Android throws a SecurityException

Example:
```Java
Intent.ACTION_BOOT_COMPLETED
```

If your app tries:
```Java
sendBroadcast(new Intent(Intent.ACTION_BOOT_COMPLETED));
```

- Without protected broadcasts:
  - Any app could pretend the phone rebooted
  - Fake incoming SMS
  - Trigger system alarms
  - Mess with power, connectivity, or security flows

## Explicit Broadcasts

```Java
Intent intent = new Intent(context, MyReceiver.class);
sendBroadcast(intent);
```

- Goes to one specific receiver
- No intent filters involved

## Implicit Broadcasts

```Java
Intent intent = new Intent("com.example.ACTION_SYNC");
sendBroadcast(intent);
```

- Goes to all matching receivers
- This is where **protected broadcasts** matter
- Restricted heavily since Android 8.0

## Restrict broadcasts with permissions

They are called **Permission-Protected Broadcasts**.

Here, sending or receiving requires a permission.

Permissions allow you to restrict broadcasts 
to the set of apps that hold certain permissions. 

You can enforce restrictions on either the sender or receiver of a broadcast.

Only receivers who have requested that permission with the `<uses-permission>` tag 
in their manifest can receive the broadcast.
