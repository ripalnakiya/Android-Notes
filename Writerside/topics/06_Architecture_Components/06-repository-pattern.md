# Repository Pattern
<show-structure depth="2"/>

It is not specific to Android - it is a common design pattern.

It is a reusable solution to a commonly occurring problem in software design.

It has is not connected to any one of architecture patterns like MVC, MVP or MVVM.

[Refer Google CodeLab](https://developer.android.com/codelabs/basic-android-kotlin-compose-add-repository#0)

## Why different layers?

Separating the code into different layers makes your app more scalable, more robust, and easier to test. 
Having multiple layers with clearly defined boundaries also 
makes it easier for multiple developers to work on the same app without negatively impacting each other.

Android's Recommended app architecture states that an app should have at least a UI layer and a data layer.

## What is a data layer?

A data layer is responsible for the business logic of your app and for sourcing and saving data for your app. 
The data layer exposes data to the UI layer using the Unidirectional Data Flow pattern. 
Data can come from multiple sources, like a network request, a local database, or from a file on the device.

An app may even have more than one source of data. When the app opens, 
it retrieves data from a local database on the device, which is the first source. 
While the app is running, it makes a network request to the second source to retrieve newer data.

By having the data in a separate layer from the UI code, 
you can make changes in one part of the code without affecting another. 
This approach is part of a design principle called separation of concerns. 
A section of code focuses on its own concern and encapsulates its inner workings from other code. 
Encapsulation is a form of hiding how the code internally works from other sections of code. 
When one section of code needs to interact with another section of code, it does it through an interface.

The UI layer's concern is displaying the data it is provided. 
The UI no longer retrieves the data as that is the data layer's concern.

The data layer is made up of one or more repositories. Repositories themselves contain zero or more data sources.

![repository-pattern-1.png](repository-pattern-1.png){ height=300 }

Best practices require the app to have a repository for each type of data source your app uses.

## What is a repository?

In general a repository class:
- Exposes data to the rest of the app.
- Centralizes changes to data.
- Resolves conflicts between multiple data sources.
- Abstracts sources of data from the rest of the app.
- Contains business logic.

## Usage of Repository Pattern in MVVM

### Normal Application Flow

![normal-application-flow.png](application-flow-normal.png){ height=400 }

Here, `ViewModel` has multiple responsibilities
- It provides data to the views
- It fetches data from different data-sources (network, database)

> This architecture doesn't follow SOLID principles, because making changes in data-sources will impact ViewModel.

### Repository Pattern Application Flow

![application-flow-repository-pattern.png](application-flow-repository-pattern.png){ height=400 }

Only **repository** will responsibility of interacting with data-sources.

> This follows SOLID principles, because making changes in the data-sources or adding a new data-source will require changes only in **repository**.

## Repository Pattern in MVVM

![repository-pattern.png](repository-pattern.png){ height=400 }

**Repository** mediates between the **domain layers** (activity, ViewModel) and **data mapping layers** (data-sources).

## Use Case

![application-flow-repository-pattern-use-case.png](application-flow-repository-pattern-use-case.png)

Repository will check if device is connected to internet
- If device is connected to internet:
  - it will make API call
  - store the result in DB
  - fetch that DB result
  - return the DB result to ViewModel 
- If device is NOT connected to internet:
  - it will fetch the DB result
  - return the DB result to ViewModel
