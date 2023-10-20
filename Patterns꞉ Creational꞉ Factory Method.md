---
tags: [Patterns]
title: 'Patterns: Creational: Factory Method'
created: '2023-10-20T08:08:32.633Z'
modified: '2023-10-20T09:04:13.438Z'
---

# Patterns: Creational: Factory Method

[refactoring.guru](https://refactoring.guru/design-patterns/factory-method)

A.k.a. _Virtual Constructor_


## Problem

- There is some common business logic in a class
- This logic initially manages a single type of objects
- With time, there arises a need to manipulate other types of objects, each having its own construction logic
- We want to separate the core business logic in the base class from client-specific object construction logic


## Terminology

**Factory method** &mdash; abstract method on the base class which is used by the core business logic; subclasses override it with their concrete construction logic.

**Product** &mdash; an object that is returned by the factory method.


## Implementation

a. Implement **product** classes:
  1. Define the base `IProduct` interface with all features that are relevant to the core business logic
  2. Implement concrete product classes that implement the `IProduct` interface

b. Implement the core business logic in the base (perhaps `abstract` class):
  1. Declare the **factory method** (commonly named using the `createProduct()` pattern):
  - **NB!** its signature must specify the `IProduct` interface as the return type
  - it should have only common creation logic, if any
  - failing any common creation logic, it can be made `abstract`
  2. Implement the core business logic methods:
  - where product objects are needed, call the **factory method**
  - manipulate objects of `IProduct` interface type instead of concrete subclass types

c. Implement concrete product construction logic in subclasses of the base class (a.k.a. _product creators_):
  1. extend the base class
  2. `override` the base class **factory method**:
  - method signature still has the `IProduct` interface as the return type
  - however, the overridden factory method must return an instance of a **concrete product class**

d. In the client code:
  1. Define a field of the base class type
  2. Depending on some condition, instantiate the appropriate **product creator** subclass and assign the returned value to this field
  3. Use the core business logic API of the base class type


## Use cases

- We don't know beforehand the exact types and dependencies of the objects our code should work with
- We want to save system resources by re-using existing objects instead of rebuilding them each time
- We'd like to provide clients of our framework with a way to extend its internal components




