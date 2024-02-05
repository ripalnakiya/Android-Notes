# ViewModel and LiveData

## ViewModel

The ViewModel class is a business logic or screen level state holder. It **exposes state to the UI** and **encapsulates related business logic**.

Its principal advantage is that it **caches state** and **persists it through configuration changes**.

## LiveData

LiveData is an **observable data holder class**.

Unlike a regular observable, **LiveData is lifecycle-aware**, meaning it respects the lifecycle of other app components, such as activities, fragments, or services.

This awareness ensures LiveData **only updates app component observers** that are in an **active lifecycle state**.

Follow these steps to work with LiveData objects:

- Create an instance of `LiveData` to hold a certain type of data. This is usually done within your ViewModel class.

- Create an Observer object that defines the `onChanged()` method, which controls what happens **when the LiveData object's held data changes**. You usually create an Observer object in a UI controller, such as an activity or fragment.

- Attach the Observer object to the LiveData object using the `observe()` method. The `observe()` method takes a LifecycleOwner object. This **subscribes the Observer object to the LiveData object** so that it is notified of changes.

> You usually attach the Observer object in a UI controller, such as an activity or fragment.

> When you update the value stored in the LiveData object, it triggers all registered observers as long as the attached LifecycleOwner is in the active state.

## Using LiveData objects

A LiveData object is usually **stored within a ViewModel object** and is accessed via a getter method.

LiveData delivers updates only when data changes, and only to active observers. 

Activities and fragments should not hold LiveData instances because their role is to display data, not hold state.