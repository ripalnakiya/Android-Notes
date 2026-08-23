# Basics of DI
<show-structure depth="2"/>

Dependency Injection is a technique where a class receives 
its dependencies from the outside instead of initializing them itself. 

By “injecting” these objects into the class, 
you separate the logic of how an object is built from the logic of how an object is used.

**Why use Dependency Injection and what are its advantages?**
We use Dependency Injection to move the responsibility of creating objects 
away from the classes that use them. 
This separation is essential for building professional, scalable Android applications.

## The Core Advantages
**Decoupling**: Classes become independent of their dependency implementations, making the system modular and easier to scale.

**Testability**: You can easily swap real components with Mock objects, allowing for isolated and reliable unit testing.

**Reusability**: Since classes aren’t responsible for object creation, they can be reused across different parts of the application without modification.

**Refactoring**: Because dependencies are provided externally, you can change a component’s implementation or its constructor without having to manually update every single place that class is used.

**Maintainability**: Centralizing object creation (via Hilt modules) means you only need to change a dependency in one place to update the entire app.

**Lifecycle Management**: DI frameworks like Hilt automatically handle when an object is created and destroyed, preventing memory leaks and managing scopes (like Activity or Fragment lifecycles) efficiently.

## What is Hilt

Hilt is Google’s recommended library for Dependency Injection in Android. 
It is built on top of Dagger, 
but it simplifies the process by handling all the “boilerplate” code for you.

While Manual DI requires you to create a manual AppContainer and wire everything by hand, 
Hilt acts as an automated "Smart Factory" that generates all that code in the background.

## Also Read:

[Medium Article by @huseyinozkoc](https://medium.com/@huseyinozkoc/android-dependency-injection-with-hilt-4b9cd4d83073)
