# ViewModel and State in Compose

When a configuration change occurs, you can save an app's data through different ways, 
such as using `rememberSaveable` or saving the instance state. 
However, these options can create problems. 

Most of the time, you can use `rememberSaveable` but that might mean keeping the logic in or near composables. 
When apps grow, you should move data and logic away from composables.

## Learn about app architecture

An app's architecture provides guidelines to help you allocate the app responsibilities between the classes. 
A well-designed app architecture helps you scale your app and extend it with additional features. 
Architecture can also simplify team collaboration.

The most common [architectural principles](https://developer.android.com/jetpack/guide#common-principles) 
are **separation of concerns** and **driving UI from a model**.

### Separation of concerns

The **separation of concerns design** principle states that the app is divided into classes of functions, 
each with separate responsibilities.

### Drive UI from a model

The **drive UI from a model** principle states that you should drive your UI from a model, preferably a persistent model. 
Models are components responsible for handling the data for an app. 
They're independent from the UI elements and app components in your app, 
so they're unaffected by the app's lifecycle and associated concerns.

## Recommended app architecture

Considering the common architectural principles mentioned in the previous section, 
each app should have at least two layers:

- **UI layer:** a layer that displays the app data on the screen but is independent of the data.
- **Data layer:** a layer that stores, retrieves, and exposes the app data.

You can add another layer, called the domain layer, 
to simplify and reuse the interactions between the UI and data layers.

![app-architecture.png](app-architecture.png){ height=250 }

> Note: The arrows in the diagrams in this guide represent dependencies between classes. 
> For example, the domain layer depends on data layer classes.

### UI layer

The role of the UI layer, or presentation layer, is to display the application data on the screen. 
Whenever the data changes due to a user interaction, such as pressing a button, 
the UI should update to reflect the changes.

The UI layer is made up of the following components:
- **UI elements:** components that render the data on the screen. You build these elements using Jetpack Compose.
- **State holders:** components that hold the data, expose it to the UI, and handle the app logic. An example state holder is ViewModel.

![ui-layer.png](ui-layer.png){ height=300 }

### ViewModel

The `ViewModel` component holds and exposes the state the UI consumes. 
The UI state is application data transformed by `ViewModel`. 
`ViewModel` lets your app follow the architecture principle of driving the UI from the model.

`ViewModel` stores the app-related data that isn't destroyed when the activity is destroyed 
and recreated by the Android framework. Unlike the activity instance, `ViewModel` objects are not destroyed. 
The app automatically retains `ViewModel` objects during configuration changes 
so that the data they hold is immediately available after the recomposition.

To implement `ViewModel` in your app, extend the `ViewModel` class, 
which comes from the architecture components library and stores app data within that class.

### UI State

The UI is what the user sees, and the UI state is what the app says they should see. 

The UI is the visual representation of the UI state. 
Any changes to the UI state immediately are reflected in the UI.

![ui-composition.png](ui-composition.png){ height=150 }

> UI is a result of binding UI elements on the screen with the UI state.

```kotlin
// Example of UI state definition

data class NewsItemUiState(
    val title: String,
    val body: String,
    val bookmarked: Boolean = false,
    ...
)
```

### Immutability

The UI state definition in the example above is immutable. 

Immutable objects provide guarantees that multiple sources do not alter the state of the app at an instant in time. 

This protection frees the UI to focus on a single role: reading state and updating UI elements accordingly. 

Therefore, you should never modify the UI state in the UI directly, 
unless the UI itself is the sole source of its data. 

Violating this principle results in multiple sources of truth for the same piece of information, 
leading to data inconsistencies and subtle bugs.

## Unidirectional Data Flow

A unidirectional data flow (UDF) is a design pattern in which state flows down and events flow up. 

By following unidirectional data flow, 
you can decouple composables that display state in the UI from the parts of your app that store and change state.

The UI update loop for an app using unidirectional data flow looks like the following:
- **Event:** Part of the UI generates an event and passes it upward—such as a button click passed 
  to the ViewModel to handle—or an event that is passed from other layers of your app, 
  such as an indication that the user session has expired.
- **Update state:** An event handler might change the state.
- **Display state:** The state holder passes down the state, and the UI displays it.

![udf-pattern.png](udf-pattern.png){ height=300 }

The use of the UDF pattern for app architecture has the following implications:
- The `ViewModel` holds and exposes the state to the UI consumes.
- The UI state is application data transformed by the `ViewModel`.
- The UI notifies the `ViewModel` of user events.
- The `ViewModel` handles the user actions and updates the state.
- The updated state is fed back to the UI to render.
