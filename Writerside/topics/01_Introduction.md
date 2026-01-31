# Introduction

Android is an **open-source operating system** primarily designed for mobile devices such as smartphones and tablets.

It is based on the **Linux kernel** and developed by a consortium of developers known as the Open Handset Alliance, **led by Google**.

Android is written in **Java** and Kotlin programming languages.

## Gradle

Android Studio IDE uses **Gradle** as its build system.

Gradle is a powerful and flexible build automation tool used in software development, particularly in the Java and Android ecosystems.

It is designed to manage the **build lifecycle of projects**, _automate_ the **compilation and packaging processes**, and **handle dependencies** efficiently.

Gradle is the tool working behind the scenes to **compile and package your app**.

It **looks at the dependencies** you _declared in your build.gradle_ files and creates a build script accordingly.

## SDK

SDK stands for Software Development Kit.

It is a set of tools, libraries, and documentation that developers use to create software applications for specific platforms.

## Performance Issues

ANR

- Application Not Responding
- Long Running Tasks in UI thread

Crash

- When an exception is not handled properly

Memory Leaks

- When an object is no longer needed, but it is still referenced by other objects.
- When Garbage collector does not free up an object in your app that can't be used anymore
- **Solution**: Use LeakCanary library

## Multi Module App

It is a Practice of breaking the concept of a monolithic, one-module codebase into loosely coupled, self contained modules.

There are two ways of Modularization:

1. Modularization by Layer
2. Modularization by Feature

> But to make a better app, we can use Modularization by Layer and Feature, both
>
> Making the app scalable
>
> Some Modules are => `:app`, `:feature:foryou`, `:core:data`, `:core:datastore`, `:core:network`, `:build-plugins`

There are three features of a good app architecture:

1. Cohesion - Modules have Well Defined Responsibilities
2. Coupling - Modules have Least Dependancy on Other Modules
3. Granularity - The extent to which the codebase is composed of modules

## Reducing App Size and Securing the App

To make your app as small as possible, you should enable `shrinking` in your release build to remove unused code and resources.

When enabling `shrinking` :

- you also benefit from **obfuscation**, which shortens the names of your app’s classes and members
- and **optimization**, which applies more aggressive strategies to further reduce the size of your app.
