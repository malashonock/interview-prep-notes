---
tags: [RxJs]
title: 'RxJs: Operators overview'
created: '2023-10-09T12:24:42.719Z'
modified: '2023-10-09T13:35:47.358Z'
---

# RxJs: Operators overview

All operators are functions.

There are 2 kinds of operators:
- pipeable operators
- creation operators


## Pipeable operators

Used to transform the input stream.

Pipeable = various pipeable operators can be put together to form a pipe of data transformations.

**Pipeable operator** is a _pure_ fn that:
- takes an observable as its input
- doesn't change the input observable!
- returns a new output observable

Subscribing to the output observable will also subscribe to the input observable.

**Pipeable operator _factory_** is a fn that can accept params to set the context and returns a pipeable operator &mdash; mostly, operator factories are used.

**Tip**: Piping is done via `pipe(opf1(), opf2(), ...)` method (even if there is only one operator in chain).


## Creation operators

**Creation operator** is a static fn that creates a new observable:
- either using non-observable params, e.g.:
  - `of()`
  - `from()`
  - `timer()`
  - `interval()`
  - `fromEvent()`
- or by joining other observables, e.g.:
  - `combineLatest()`
  - `forkJoin()`
  - `concat()`
  - `merge()`

[Creation operators list](https://rxjs.dev/guide/operators#creation-operators-list)

