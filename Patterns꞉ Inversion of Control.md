---
tags: [Patterns]
title: 'Patterns: Inversion of Control'
created: '2023-10-19T14:45:14.733Z'
modified: '2023-10-19T14:56:38.904Z'
---

# Patterns: Inversion of Control

[Definition by M.Fowler](https://martinfowler.com/bliki/InversionOfControl.html)

Without Inversion of Control (IoC), the app code directly _controls_ every aspect of the program flow.  
**Example**: a simple console app that displays prompts and accepts user input.

With IoC, the app _delegates_ some parts of the program flow to some external code.  
**Example**: a GUI app that delegates rendering to a UI framework and only hooks into the events provided by the framework.

IoC is a.k.a. _the Hollywood Principle &mdash; "Don't call us, we'll call you"_, i.e. it's not the app that is calling the UI framework's methods &mdash; it's the UI framework that uses the app's code.


## Library vs. Framework

Library = no IoC: we write code that uses some of the classes provided by the library.  

Framework = IoC: it's the framework that holds control of program execution. Our code just extends and configures the framework and is called by the framework at the time defined by the framework.


## IoC vs. DI

IoC is a broad concept; DI is a specific facet of IoC.

