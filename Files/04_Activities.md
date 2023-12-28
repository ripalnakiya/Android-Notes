# Activities

Activity is a single, focused thing that the user can do.

It represents a single screen with a user interface.

Almost all activities interact with the user, so the Activity class takes care of creating a window for you in which you can place your UI with setContentView(View).

## Activity Lifecycle

![Activity Lifecycle](../Images/activity_lifecycle.png)

The Activity class provides the following callbacks that you can override to perform actions when the corresponding event occurs:

- **onCreate()** : It is called when the activity is first **created**, and it **initializes the essential components** of your activity, for example it **defines the UI** of the Activity.

- **onStart()** : As `onCreate()` exits, the activity enters the Started state, and the **activity becomes visible to the user**.

- **onResume()** : The system invokes this callback just **before the activity starts interacting** with the user. The activity is at the **top of the activity stack**, and captures all user input.

- **onPause()** : The system calls `onPause()` **when the activity loses focus** and enters a Paused state. for example, the **user taps the Back or Recents button**. When the system calls onPause() for your activity, it technically means your **activity is still partially visible**.

- **onStop()** : The system calls `onStop()` when the **activity is no longer visible to the user**.

- **onRestart()** : The system invokes this callback when an activity in the Stopped state is about to restart. `onRestart()` **restores the state of the activity** from the time that it was stopped.

- **onDestroy()** : The system invokes this callback **before an activity is destroyed**. `onDestroy()` is usually implemented to ensure that** all of an activity’s resources are released** when the activity is destroyed.

## Scenario 1 : Single Activity - User Navigates Away

> **Triggered By :**
>
> User presses the Home button
>
> User switches to another app (From Notification or Accepting a call)

**App Started :**

- onCreate()
- onStart()
- onResume()

**Home Button Pressed :**

- onPause()
- onStop()
- **onSaveInstanceState()**

**App Restarted :**

- onRestart()
- onStart()
- onResume()

> When the user taps the Overview (Recent Apps) or Home button, the system behaves as if the current activity has been completely covered.

## Scenario 2 : Single Activity - Configuration Changes

> **Triggered By :**
>
> Device Rotated
>
> Enabled Multi-window mode
>
> Resize the app window in Multi-window mode
>
> Changes to language settings or input device

**App Started :**

- onCreate()
- onStart()
- onResume()

**Device Rotated :**

- onPause()
- onStop()
- **onSaveInstanceState()**
- `onDestroy()`
- onCreate()
- onStart()
- **onRestoreInstanceState()**
- onResume()

> In **multi-window mode**, although there are two apps that are visible to the user, only the one the user is interacting with is in the foreground and has focus.
>
> That activity is in the Resumed state, while the app in the other window is in the Paused state.

## Scenario 3 : Single Activity - Dialog Overlapping

> **Triggered By :**
>
> Enabling Multi-window mode
>
> A runtime permission dialog
>
> An intent chooser appears (such as a share dialog)

**App Started :**

- onCreate()
- onStart()
- onResume()

**Dialog Appeared :**

- onPause()

**Closing the dialog :**

- onResume()

> This scenario doesn’t apply to :
>
> Dialogs in the same app. Showing an AlertDialog or a DialogFragment of the same app won’t pause the underlying activity.
>
> Notifications. User receiving a new notification or pulling down the notification bar won’t pause the underlying activity.

## Scenario 4 : Single Activity - call finish() in onCreate()

- onCreate()
- onDestroy()

## Scenario 5 : Multiple Activities - Navigating between Activities

**App Started (Activity A):**

- onCreate() : A
- onStart() : A
- onResume() : A

**Navigating to B :**

- onPause() : A
- onCreate() : B
- onStart() : B
- onResume() : B
- onStop() : A

**Navigating back to A :**

- onPaused() : B
- onRestart() : A
- onStart() : A
- onResume() : A
- onStop() : B
- onDestroy() : B

## Scenario 6 : Multiple Activities - Activities in the back stack with configuration changes

**App Started (Activity A):**

- onCreate() : A
- onStart() : A
- onResume() : A

**Navigating to B :**

- onPause() : A
- onCreate() : B
- onStart() : B
- onResume() : B
- onStop() : A

**Device Rotated :**

- onPause() : B
- onStop() : B
- onDestroy() : B
- onCreate() : B
- onStart() : B
- onResume() : B

**Navigating back to A : (With Rotated Device)**

- onPause() : B
- onDestory() : A
- onCreate() : A
- onStart() : A
- onResume() : A
- onStop() : B
- onDestroy() : B

## Task

A task is a **collection of activities** that users interact with when performing a certain job.

A task represents a sequence of activities that have a specific workflow or are related to a specific user action.

When activities belong to the **same task**, they are part of the same workflow and share the same task stack.

The task stack is a Last In, First Out (LIFO) structure, meaning the last activity added to the stack is the first one to be removed when the user navigates backward.

```sh
Task Stack: A -> B -> C
```

When activities belong to **different tasks**, they are independent of each other and have their own task stacks. Activities in different tasks cannot influence each other's behavior directly.

```sh
Task Stack in App 1: A -> B -> C
Task Stack in App 2: D
```

**Activity D in App 2 is in a different task.**

Even though Activity D might be in the foreground, pressing the back button would bring the user back to the task stack in App 1.

**Back Stack :** Activities are arranged with the order in which each activity is opened. This maintained stack called Back Stack.

## Activity Launch Modes

### Standard

The default and most common launch mode.

Every time there's a new intent, a new instance of the activity will be created and placed on the top of the stack.

It creates a new instance of an activity in the task from which it was started.

**State of Activity Stack before launch B**

```sh
A -> B -> C -> D
```

**State of Activity Stack after launch B**

```sh
A -> B -> C -> D -> B
```

### SingleTop

If an instance of the activity already exists at the top of the stack, the system routes the intent to that instance through a call to its `onNewIntent()` method, rather than creating a new instance of the activity.

If an instance is not present on top of task then new instance will be created.

**State of Activity Stack before launch D**

```sh
A -> B -> C -> D
```

**State of Activity Stack after launch D activity**

```sh
A -> B -> C -> D
```

(Here old instance gets called and intent data route through onNewIntent() callback)

> Using this launch mode you can create multiple instance of the same activity in the same task or in different tasks
>
> **only if** the same instance does not already exist at the top of stack.

### SingleTask

The singleTask launch mode is used to ensure that only one instance of the activity exists in a task.

**State of Activity Stack before launch B**

```sh
A -> B -> C -> D
```

**State of Activity Stack after launch B activity**

```sh
A -> B
```

(Here old instance gets called and intent data route through onNewIntent() callback)

Also notice that C and D activities get destroyed here.

### SingleInstance

It is similar to singleTask except that no other activities will be created in the same task. Any other activity started from here will be created in a new task.

> It used in the applications that has only one activity.

Suppose, activity D has “launch mode = singleInstance”

**State of Activity Stack before launch D**

```sh
A -> B -> C
```

**State of Activity Stack after launch D activity**

```sh
Task1 — A -> B -> C
Task2 — D (here D will be in different task)
```

**Continue this and start E on D**

```sh
Task1 — A -> B -> C -> E
Task2 — D
```
