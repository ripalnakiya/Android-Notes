# Broadcast

Android apps can send or receive broadcast messages from the Android system and other Android apps, similar to the **publish-subscribe design pattern**.

Broadcasts can be used as a messaging system across apps and outside of the normal user flow.

The broadcast **message itself is wrapped in an Intent** object.

A **broadcast receiver** is an Android component that **allows the registration and handling of system-wide or application-wide broadcast messages**, also known as intents.

## Static

```xml
<receiver
    android:name=".AirplaneModeChangeReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.AIRPLANE_MODE"/>
    </intent-filter>
</receiver>
```

## Dynamic

**Registering the Receiver Dynamically:**

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

> Dynamic Broadcasts are also known as Context based Broadcast

## Custom Broadcast

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

> Create the onReceive() method in the CustomBroadcastReceiver class

Unregistering the receiver:

```Java
unregisterReceiver(customBroadcastReceiver);
```
