---
tags: [Patterns]
title: 'Patterns: Behavioral: Template Method'
created: '2023-10-20T07:37:57.645Z'
modified: '2023-10-20T09:02:03.808Z'
---

# Patterns: Behavioral: Template Method

[refactoring.guru](https://refactoring.guru/design-patterns/template-method)


## Problem

- There is some algorithm that consists of a number of steps
- Some steps are client-specific &mdash; we want do delegate their implementation to the consumer
- Other steps are common for all consumers &mdash; we don't want do duplicate the same code


## Terminology

**Template method** &mdash; the method that represents the main algorithm.


## Implementation

a. Declare an `abstract` base class, in which:
  1. Implement the _algorithm_ as a series of method calls in a `public sealed` (C#) method (a.k.a. the **template method**)
  2. Implement **common** steps as `protected virtual` methods:
  - implement common behavior in the abstract base class
  - subclasses can override it as needed
  3. Declare **client-specific** steps as **`abstract`** methods:
  - template method calls them, but they will be implemented at the consumer
  
b. Declare concrete subclasses of the above base class, in which:
  1. `override` the `abstract` client-specific step methods of the base class
  2. Optionally, `override` those common step methods that should have a different behavior in this particular subclass


## Use cases

- We want client code to implement **some** steps of the algorithm, but not the entire algorithm
- We have multiple classes that contain almost identical algorithms with minor differences

