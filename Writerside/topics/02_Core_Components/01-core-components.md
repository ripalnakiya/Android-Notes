# App Components

App components are the essential **building blocks** of an Android app.

Each component is an **entry point through which the user can enter** your app.

There are **four** main components, 
**Each type serves a distinct purpose and has a distinct lifecycle** 
that defines how a component is created and destroyed.

## Context

Context is an object that provides information 
about the **current state of the application** and 
facilitates **access to application resources** and system-level operations.

- Using a Context, you can:
  - Access resources (strings, layouts, drawables)
  - Start activities and services
  - Get system services (LayoutInflater, NotificationManager, etc.)
  - Access application assets
  - Show toasts, dialogs, notifications
  - Access preferences, databases, file system

For example,

**Accessing Resources**

```Java
// Accessing a string resource
String appName = context.getString(R.string.app_name);
```

**Accessing System Services**

```Java
// Accessing the Alarm Service
AlarmManager alarmManager = getSystemService(AlarmManager.class);
```

**Accessing App Components**

```Java
// Starting an Activity
Intent intent = new Intent(context, MainActivity.class);
context.startActivity(intent);
```

## Types of Context

### 1. Application Context

- Lifetime: entire app process
- Tied to: `Application`
- Use when you need something that should live as long as the app

- Examples:
  - Starting a Service
  - Accessing shared preferences
  - Long-lived objects (singletons, repositories)

```Java
Context appContext = getApplicationContext();
```

### 2. Activity Context

- Lifetime: as long as the Activity
- Tied to: UI and theme
- Required for anything that touches the screen

- Examples:
  - Inflating layouts
  - Showing dialogs
  - Starting another Activity

```Java
Context activityContext = this;
```

**Use this for UI.** Period.

### 3. Service Context

- Lifetime: as long as the Service
- Similar to Application Context, but scoped to the service

- Used when:
  - You’re inside a Service and need context-aware operations

## Mistakes when using the Context

- **Using Application Context for UI stuff**
  Enjoy your crashes and un-themed dialogs.

- **Holding Activity Context in static variables**
Congrats, you invented a memory leak.
