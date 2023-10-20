---
tags: [Patterns]
title: 'Patterns: Behavioral: Strategy'
created: '2023-10-20T13:12:27.991Z'
modified: '2023-10-20T13:25:19.309Z'
---

# Patterns: Behavioral: Strategy

[refactoring.guru](https://refactoring.guru/design-patterns/strategy)


## Terminology

**Strategy** &mdash; one of the ways to implement an algorithm.

**Context class** &mdash; a class that uses a strategy as part of its own business logic.


## Problem

- There are many ways to implement a feature or an algorithm
- If all these ways are implemented in the context class, it quickly grows in size and becomes too hard to maintain
- You want to separate feature implementation from the main business logic


## Implementation

1. Define a common `IStrategy` interface
2. Implement concrete strategy classes implementing the `IStrategy` interface
3. In the context class:
  - define a field of generic `IStrategy` type
  - implement a method to assign a `IStrategy` to this field (via Ctor or setter)
  - call the `IStrategy` APIs in context class's business logic as needed
4. In the client code:
  - Pass an instance of the selected _concrete_ strategy class to the instance of the context class
  - Call the APIs of the context class as needed


## Use cases

- There are many ways to implement an algorithm, and you want to be able to swap these ways at runtime
- There are a lot of similar classes that only differ in the way they execute some particular behavior
- To separate the core business logic of a class from the various implementations of a non-core algorithm that is of secondary importance
- If a class has a massive `switch` somewhere that chooses among the variants of the same algorithm
