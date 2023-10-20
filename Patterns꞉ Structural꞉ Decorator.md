---
tags: [Patterns]
title: 'Patterns: Structural: Decorator'
created: '2023-10-20T12:36:47.658Z'
modified: '2023-10-20T12:57:39.643Z'
---

# Patterns: Structural: Decorator

A.k.a. _Wrapper_


## Problem

- There are many ways to enhance the behavior of a class, and these ways can be combined freely
- Implementing these behavior enhancements in subclasses using behavior is not a viable option
- By using inheritance, the number of subclasses will quickly go through the roof


## Implementation

a. Define a common interface:
  - It should match the interface of the wrapped class
  - Any decorator must implement this interface as well

b. Implement the base `Decorator` class:
  1. The decorator class must implement the same common interface as the wrapped class (_wrappee_)
  2. Store reference to the wrappee instance in a decorator class field (assign in the Ctor of the decorator class)
  3. Redirect all calls to the methods from the wrappee interface to the namesake methods on the wrappee instance itself

c. Implement concrete `Decorator` subclasses:
  - `override` methods of the wrappee class:
    - call namesake methods on `super` when and where needed
    - enhance superclass's logic by calling own behavior either before or after calling the overridden method of the superclass

d. In client code:
```
a = new Wrappee();
b = new ConcreteDecorator1(a);
c = new ConcreteDecorator2(b);
c.executeSomeLogic();
```


## Use cases

- You want to assign extra behavior to objects at runtime without breaking the client code
- It's awkward or not possible at all (`sealed`) to use class inheritance
