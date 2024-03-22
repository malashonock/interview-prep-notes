---
tags: [RxJs]
title: 'RxJs: Observables overview'
created: '2023-10-09T08:42:51.019Z'
modified: '2023-10-09T09:52:21.970Z'
---

# RxJs: Observables overview

[Observables overview](https://rxjs.dev/guide/observable)
[RxJs glossary](https://rxjs.dev/guide/glossary-and-semantics)


## Definitions

An observable is:
- a lazy push collection of multiple values
- a template for connecting an Observer (as a Consumer) to a Producer, via a `subscribe` action, resulting in a subscription


## Creating observables

**Option 1**: direct construction:
```
const observable = new Observable(
  function subscribe(observer) {
    // Observable execution logic

    return function unsubscribe() {
      // Resource cleanup
    };
  }
);
```

**Option 2** (more often): via creation fns, e.g.:
- `of`
- `from`
- `fromEvent`
- `interval`
- `timeout`
- ...


## Subscribing to observables

An `observer` is just an object with 3 callbacks:
- `next(...)`
- `error(...)`
- `complete()`

To subscribe, pass an `observer` object to the `subscribe(...)` fn of the observable:
```
const subscription = observable.subscribe({
  next(value) {
    // Processing values
  },
  error(err) {
    // Error handling
  },
  complete() {
    // Handling complete notification
  },
});
```

**NB!** Not all 3 callbacks can be specified &mdash; in this case, the observer is called _partial_.

**Tip**: If only `next` callback is specified, it can be passed directly, i.e. without wrapping in an object.

**NB!** `subscribe(...)` calls are **not** shared among multiple observers &mdash; each call triggers its own setup for that given observer (a.k.a. a _unicast_ subscription).


## Executing Observables

A call to `subscribe(...)` will start an observable execution.

**Observable grammar / contract** (as a RegExp):
```
next*(error|complete)?
```
- zero, or one, or many `next` notifications
- either an `error`, or a `complete` notification can be emitted
- `error`/`complete` are optional
- after `error`/`complete`, there can be no more `next` notifications
- after `error`/`complete` or `unsubscribe()`, `finalization` is triggered which cleans up the resources


## Disposing observable executions

A call to `subscribe(...)` returns a `Subscription`.

Once the observer is no longer interested in observing the Observable, call `unsubscribe()` on the subscription object:
```
subscription.unsubscribe();
```


