---
tags: [Patterns]
title: 'Patterns: Dependency Injection'
created: '2023-10-19T14:56:47.488Z'
modified: '2023-10-19T15:07:21.952Z'
---

# Patterns: Dependency Injection

[Article by M.Fowler](https://martinfowler.com/articles/injection.html)

DI is a subset of IoC.

**Problem**: Some methods of a class call methods of other classes (referred to as _dependencies_).  
The class has to manage the creation and lifecycle of its dependencies on its own.  
Also, it's impossible to change implementation of dependencies without changing the main class.  
This all leads to **high coupling** between the class and its dependencies.

**Solution**:
- Declare class dependencies in class fields that e.g. are initialized in the class Ctor (_constructor injection_)
- Delegate the creation of dependencies to an external object (a.k.a. _DI container_)
- Some external (_framework?_) code will setup the DI container and instantiate application classes
