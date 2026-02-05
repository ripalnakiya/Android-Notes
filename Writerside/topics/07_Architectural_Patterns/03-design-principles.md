# Design Principles

These are fundamental software design metrics or properties used to evaluate code structure.

## 1. Coupling

Coupling = how much one module depends on another module.

If changing class A forces you to touch class B, they’re coupled.

### Tight Coupling

- Classes know too much about each other
- Direct instantiation
- Hard dependencies

```Java
class OrderService {
    private PaymentGateway gateway = new PaymentGateway();
}
```

- Problems
  - Hard to test
  - Hard to change
  - Hard to reuse
  - Everything breaks together

- Android example
  - Activity directly calling Retrofit
  - Fragment manipulating database entities

### Loose Coupling

- Depends on abstractions, not implementations
- Uses interfaces, DI, callbacks

```Java
class OrderService {
    private PaymentGateway gateway;

    OrderService(PaymentGateway gateway) {
        this.gateway = gateway;
    }
}
```

- Benefits
  - Easy testing
  - Easy replacement
  - Independent evolution

- Android example
  - ViewModel → Repository interface
  - Hilt / Dagger doing the wiring

## 2. Cohesion

Cohesion = how closely related the responsibilities inside a module are.

"Does this class do one clear thing ?"

### High Cohesion

- Single, focused responsibility
- All methods serve one purpose

```Java
class UserValidator {
    boolean isEmailValid(String email) { }
    boolean isPasswordStrong(String password) { }
}
```

- Benefits
  - Easier to understand
  - Easier to test
  - Easier to reuse

- Android example
  - Repository only handles data
  - ViewModel only handles UI state

### Low Cohesion

- Unrelated responsibilities mashed together

```Java
class UserManager {
    void login() {}
    void saveToDb() {}
    void drawButton() {}
    void sendAnalytics() {}
}
```

- Problems
  - Hard to maintain
  - Change one thing, break five
  - Reads like a cry for help

## 3. Granularity

Granularity = size and scope of components.

"How big is a module, class, function, or API?"

### Fine-Grained

- Definition
  - Small, specific units
  - Many small classes/functions

- Example
  - One use case per class
  - One responsibility per function

- Pros
  - Reusable
  - Testable
  - Flexible

- Cons
  - Too many files
  - Over-abstraction if abused

- Android example
  - Separate UseCases
  - Small composables

### Coarse-Grained

- Definition
  - Larger units
  - Fewer, bigger components

- Example
  - One service doing multiple operations

- Pros
  - Simpler navigation
  - Less boilerplate

- Cons
  - Less reusable
  - Harder to change safely

## Summary

Coupling, cohesion, and granularity are fundamental software design principles 
used to evaluate and improve code structure and maintainability.

- Good design aims for:
  - Low Coupling
  - High Cohesion
  - Appropriate Granularity

How They Relate to Other Concepts:

```
Software Design Principles
 ├── Coupling
 ├── Cohesion
 └── Granularity
        ↓
Influence
 ├── Architecture (MVVM, Clean, MVI)
 ├── Design Patterns
 └── Code Quality
```
