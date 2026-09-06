# Android Architecture Patterns
<show-structure depth="2"/>

Need of Architecture:
- Scalability & Maintainability of application
- To achieve this - we implement architecture patterns - MVC, MVP, MVVM etc.
- Architecture patterns mainly focuses on:
  - Separation of concerns
  - Unit testing

## 1. Model View Controller

- Idea
    - **View:** UI (XML + Activity/Fragment)
    - **Controller:** handles user input and updates model
    - **Model:** data + business logic

- Android reality
    - Activities and Fragments become **View + Controller**.
    - Tight coupling.
    - Hard to test.
    - Still **fine for tiny apps** or prototypes.


## 2. Model View Presenter

- Idea
    - **View:** Activity/Fragment (UI)
    - **Presenter:** handles logic, talks to View via interface
    - **Model:** data layer

- Why it existed
    - Because Activities were doing too much.

- Pros
    - Better separation than MVC
    - Testable Presenters
    - Clear responsibilities

- Cons
    - Tons of boilerplate
    - Presenter lifecycle overhead
    - **Manual state** handling

## 3. Model View ViewModel

- Idea
    - **View:** Activity/Fragment/Compose UI
    - **ViewModel:** UI-related state + logic
    - **Model:** repository, data sources

- Uses:
    - LiveData / StateFlow
    - Data Binding or Compose

- Pros
    - Clean separation
    - Testable ViewModels
    - Works great with Compose

> We even break the **Model** into **Repository** and **DataSources** to follow the repository-pattern.

### View

- **What it is**
    - UI only
    - Displays data
    - Sends user actions upward

- **Android examples**
    - Activity
    - Fragment
    - Jetpack Compose UI
    - XML layouts

- **Responsibilities**
    - Observe state
    - Render UI
    - Forward user events

- **What it must NOT do**
    - Business logic
    - Network calls
    - Database operations
    - Decision making beyond basic UI stuff


### ViewModel

- **What it is**
    - Holds UI-related state
    - Handles user actions
    - Talks to Model
    - Survives configuration changes

**Key idea:** ViewModel does NOT know the View exists.

No references to `Activity`, `Fragment`, `Context` (unless `Application` is explicitly justified).

- **Android ViewModel responsibilities**
    - Expose state via LiveData, StateFlow, or Compose State
    - Process events from View
    - Call repositories / use cases
    - Transform data into UI-friendly form


### Model

- **What it is**
    - Business logic
    - Data layer
    - Rules of the app

- **In Android terms**
    - Repositories
    - Data sources (Room, Retrofit, DataStore)
    - Domain models
    - Use cases (if you’re being disciplined)

- **What it must NOT know**
    - Activities
    - Fragments
    - Views

### Data flow in MVVM

![MVVM](mvvm_components.png)

- User interacts with View
- View calls a method on ViewModel
- ViewModel updates state
- View observes state change
- UI re-renders

### Why Google recommends it

1. Configuration changes
    - ViewModel survives rotation

2. Lifecycle awareness
    - LiveData / Flow respect lifecycle
    - Fewer memory leaks

3. Testability
    - ViewModel can be unit tested without Android framework

4. Jetpack support

[Refer this project](https://github.com/ripalnakiya/Android-Practice/tree/main/09_MVVM)
