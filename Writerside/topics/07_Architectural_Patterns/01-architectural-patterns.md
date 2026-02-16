# Architectural Patterns

- Architecture patterns mainly focuses on:
  - Separation of concerns
  - Unit testing

## 1. MVC (Model–View–Controller)

- Idea
  - **Model:** data + business logic
  - **View:** UI (XML + Activity/Fragment)
  - **Controller:** handles user input and updates model

- Android reality
  - Activities and Fragments become **View + Controller**.
  - Tight coupling.
  - Hard to test.
  - Still fine for tiny apps or prototypes.

## 2. MVP (Model–View–Presenter)

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

## 3. MVVM (Model–View–ViewModel)

- Idea
  - **View:** Activity/Fragment/Compose UI
  - **ViewModel:** UI-related state + logic
  - **Model:** repository, data sources

- Uses:
  - LiveData / StateFlow
  - Data Binding or Compose

- Why Google recommends it
  - Lifecycle-aware
  - Survives configuration changes
  - Fits Jetpack nicely

- Pros
  - Clean separation
  - Testable ViewModels
  - Works great with Compose

## 4. MVI (Model–View–Intent)

- Idea: Unidirectional data flow.
  - **Intent:** user actions
  - **Model:** immutable state
  - **View:** renders state

- Popular with
  - Compose
  - StateFlow
  - Redux enjoyers

- Pros
  - Predictable
  - Easy debugging
  - No mystery state changes

- Cons
  - Boilerplate
  - Overkill for small apps
  - Steep learning curve
