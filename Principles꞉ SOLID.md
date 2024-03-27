---
tags: [Principles]
title: 'Principles: SOLID'
created: '2024-03-27T11:21:07.465Z'
modified: '2024-03-27T11:29:41.094Z'
---

# Principles: SOLID

[Good explanation](https://codefinity.com/blog/The-SOLID-Principles-in-Software-Development)

- **Single responsibility**: a class should have only 1 responsibility (=job), hence only 1 reason to change
- **Open/closed**: a class should be open for extension, but closed for modification, i.e. one should be able to add new functionality without changing the existing
- **Liskov substitution**: instances of the superclass must be replaceable with instances of its subclasses
- **Interface segregation**: clients should not be forced to depend on the interfaces they don't use, i.e. split large interfaces into smaller ones
- **Dependency inversion**:
  - high-level modules should depend on low-level modules, not the other way round
  - details (=concrete classes) should depend on abstractions (=interfaces), not on the other details (concrete classes), not the other way round
