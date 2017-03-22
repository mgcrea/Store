# <img src="https://raw.githubusercontent.com/alexdrone/Dispatch/master/docs/logo.png" alt="Dispatch" />

[![Swift](https://img.shields.io/badge/swift-3-orange.svg?style=flat)](#)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Platform](https://img.shields.io/badge/platform-ios|macos|tvos|watchos-lightgrey.svg?style=flat)](#)
[![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)](https://opensource.org/licenses/MIT)

*A lightweight, thread-safe, multi-store Flux implementation in Swift.*


**Dispatch** is a [Flux](https://facebook.github.io/flux/docs/overview.html)-like implementation of the unidirectional data flow architecture in Swift.
Flux applications have three major parts: the dispatcher, the stores, and the views.

These should not be confused with Model-View-Controller. Controllers do exist in a Flux application, but they are controller-views — views often found at the top of the hierarchy that retrieve data from the stores and pass this data down to their children. 

Dispatch eschews MVC in favor of a unidirectional data flow. When a user interacts with a view, the view propagates an action through a central dispatcher, to the various stores that hold the application's data and business logic, which updates all of the views that are affected. 

This works especially well with [Render](https://github.com/alexdrone/Render)'s declarative programming style, which allows the store to send updates without specifying how to transition views between states.

- **Stores**: Holds the state of your application. You can have multiple stores for multiple domains of your app.
- **Actions**: You can only perform state changes through actions. Actions are small pieces of data that describe a state change. By drastically limiting the way state can be mutated, your app becomes easier to understand and it gets easier to work with many collaborators.
- **Dispatcher**: Dispatches an action to the stores that respond to it.
- **Views**: A simple function of your state. This works especially well with [Render](https://github.com/alexdrone/Render)'s declarative programming style.

<img src="https://raw.githubusercontent.com/alexdrone/Dispatch/master/docs/diag.png" width="640" alt="Diagram" />

# Single Dispatcher 

The dispatcher is the central hub that manages all data flow in your application. It is essentially a registry of callbacks into the stores and has no real intelligence of its own — it is a simple mechanism for distributing the actions to the stores. Each store registers itself and provides a callback. When an action creator provides the dispatcher with a new action, all stores in the application receive the action via the callbacks in the registry - and redirect the action to their reducer.

As an application grows, the dispatcher becomes more vital, as it can be used to manage dependencies between the stores by invoking the registered callbacks in a specific order. Stores can declaratively wait for other stores to finish updating, and then update themselves accordingly.

# Stores 

Stores contain the application state and logic. Their role is somewhat similar to a model in a traditional MVC, but they manage the state of many objects — they do not represent a single record of data like ORM models do. More than simply managing a collection of ORM-style objects, stores manage the application state for a particular domain within the application.

As mentioned above, a store registers itself with the dispatcher. The store has a reducer that tipically has a switch statement based on the action's type. This allows an action to result in an update to the state of the store, via the dispatcher. After the stores are updated, they notify the observers that their state has changed, so the views may query the new state and update themselves.