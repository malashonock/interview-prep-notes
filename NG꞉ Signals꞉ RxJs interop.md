---
tags: [Angular/signals, Angular/v17+]
title: 'NG: Signals: RxJs interop'
created: '2024-01-04T14:18:55.006Z'
modified: '2024-06-11T07:22:22.265Z'
---

# NG: Signals: RxJs interop

[angular.dev](https://angular.dev/guide/signals/rxjs-interop)


## `toSignal()`

Creates a signal that tracks the value of an observable.

**NB!** `toSignal()` creates a subscription - avoid calling it repeatedly, rather reuse the signal it returns.


### Initial values

**Problem**: signals always need an initial value; observables may not emit synchronously on subscription.

Possible solutions:
- by default, resulting signal value will be `undefined`
- `initialValue` option can be passed to `toSignal()`
- `requireSync` option - for observables that emit synchronously (e.g. `BehaviorSubject`)


### Errors in observables

If the underlying observable emits an error, the error is thrown when the derived signal is read.


### Completion of observables

If/when the underlying observable completes, the signal getter continues to return the most recently emitted value.


## `toObservable()`

Creates an observable that tracks the value of a signal (via an effect).



**NB!** `toObservable()` needs to be run in an injection context, i.e. class Ctor, class field, or by manually passing an injector within options.

**Note**:
 - on subscription, the 1st value is emitted synchronously (the underlying effect leverages `ReplaySubject`)
 - subsequent emits are **async**, because signals notify their consumers asynchronously during CD

