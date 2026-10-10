# Introduction
<show-structure depth="2"/>

- It is a Native UI toolkit written in Kotlin.
- Not part of the android framework (Unlike views), it is a Jetpack Library
- Basic Building block - Composable (Kotlin function with `@Composable` annotation)

## Imperative vs Declarative

- **Imperative** = “How to do it”
- **Declarative** = “What it should look like”

### Imperative UI

**Old Android Views**

You manually control _every step_.

```Kotlin
val button = findViewById<Button>(R.id.btn)

button.text = "Click me"
button.setOnClickListener {
    button.text = "Clicked"
}
```

- What’s happening:
  - You find the view
  - You mutate it
  - You tell it exactly what to do, step-by-step

### Declarative UI

**Jetpack Compose**

You describe the UI based on **state**, and the system figures out the rest.

```Kotlin
var clicked by remember { mutableStateOf(false) }

Button(onClick = { clicked = true }) {
    Text(if (clicked) "Clicked" else "Click me")
}
```

- What’s happening:
  - You declare: “If clicked = true → show this UI”
  - Compose automatically redraws UI when state changes

| Aspect         | Imperative (Views)           | Declarative (Compose)      |
|----------------|------------------------------|----------------------------|
| Control        | You control everything       | Framework controls updates |
| UI Updates     | Manual (`setText`, `notify`) | Automatic (state-driven)   |
| State Handling | Messy, scattered             | Centralized                |
| Bugs           | Easy to miss updates         | Easier to reason           |

- **Imperative:** Change state → YOU update UI
- **Declarative:** Change state → UI updates itself

> Jetpack Compose is built around composable functions. 
> 
> These functions let you define your app's UI programmatically by describing how it should look, 
> rather than focusing on the process of the UI's construction.

## Composable function

```Kotlin
@Composable
fun Greeting(name: String) {
    Text("Hello $name")
}
```

- This is NOT:
  - a view
  - not a widget

**It’s more like:** A function that describes UI

## State

> A state is any value in an app that can change over time.

All Android apps display state to the user.

A few examples of state in Android apps include:
- A message that shows when a network connection can't be established.
- Forms, such as registration forms. The state can be filled and submitted.
- Tappable controls, such as buttons.
  - The state could be
    - _not tapped_,
    - _being tapped_ (display animation),
    - _or tapped_ (an `onClick` action).

## Inheritance Tree

- All the views inherit from the View class.
- For large codebases, Inheritance becomes complex.

Jetpack Compose chooses **composition over inheritance**.
That means we compose our UI by including different UI elements.