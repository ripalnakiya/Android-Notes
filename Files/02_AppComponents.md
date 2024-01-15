# App Components

App components are the essential **building blocks** of an Android app.

Each component is an **entry point through which the user can enter** your app.

There are **four** main components, **Each type serves a distinct purpose and has a distinct lifecycle** that defines how a component is created and destroyed.

## Activity

An activity is the **entry point** for interacting with the user.

It represents a **single screen** with a user interface.

## Service

A service is a **general-purpose entry point** for keeping an app running in the background for all kinds of reasons.

It is a **component that runs in the background** to perform **long-running operations** or to perform work for **remote processes**.

It **does not** provide a **user interface**.

- A service might **play music in the background** while the user is in a different app
- It might **fetch data over the network** without blocking user interaction with an activity

there are **two types of services** that **tell the system how to manage an app**:

### Started Services

A **started service** is one that **another component starts** by calling `startService()`, which results in a call to the service's `onStartCommand()` method.

It tell the system to keep them running **until their work is completed**.

Once started, a service can **run in the background indefinitely**, even if the component that started it is destroyed.

Usually, a started service performs a **single operation** and **does not return a result** to the caller.

For example, it might **download or upload a file** over the network.

When the operation is done, the service should **stop itself**.

> If a component starts the service by calling `startService()` (which results in a call to `onStartCommand()`), the service continues to run until it stops itself with `stopSelf()` or another component stops it by calling `stopService()`.

### Bound Services

A **bound service** is one that **another component binds to** by calling `bindService()`, which allows the client to **interact with the service**, send requests and receive results.

A bound service **runs only as long as another application component** is bound to it.

Multiple components can **bind to the service at once**, but when all of them unbind, the service is destroyed.

> f a component calls `bindService()` to create the service and `onStartCommand()` is not called, the service runs only as long as the component is bound to it. After the service is unbound from all of its clients, the system destroys it.

## Broadcast Receivers

A broadcast receiver is a **component that enables the system to deliver events to the app outside of a regular user flow** so the app can respond to system-wide broadcast announcements.

The system can deliver broadcasts even to apps that aren't currently running.

## Content Providers

A content provider manages a **shared set of app data** that you can store in the file system, in a SQLite database, on the web, or on any other persistent storage location that your app can access.

Through the content provider, other apps can query or modify the data, if the content provider permits it.

## Context

Context is an object that provides information about the **current state of the application** and facilitates **access to application resources and system-level operations**.

For example,

**It is used for accessing Resources**

```java
// Accessing a string resource
String appName = context.getString(R.string.app_name);
```

**It is used for accessing System Services**

```java
// Accessing the Alarm Service
AlarmManager alarmManager = getSystemService(AlarmManager.class);
```

**It is used for accessing App Components**

```java
// Starting an Activity
Intent intent = new Intent(context, MainActivity.class);
context.startActivity(intent);
```
