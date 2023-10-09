---
tags: [RxJs]
title: 'RxJs: Higher-order observables'
created: '2023-10-09T12:40:14.758Z'
modified: '2023-10-09T13:14:12.998Z'
---

# RxJs: Higher-order observables

**Higher-order observable** (HOO) &mdash; an observable that emits other observables.

HOOs are usually flattened into first-order observables.


## Typical flattening operators


### `concatAll()` / `concatMap(...)`

[`concatAll()`](https://rxjs.dev/api/operators/concatAll) outputs the inner observables (created by the source observable) **one after the other**.

`concatMap(...)` = `map(...)` + `concatAll()`

```
// Creates a source observable that emits on every click
const sourceObs = fromEvent(document, 'click');

// Creates an observable that emits 4 values every second
const innerObsFactory = () => interval(1000).pipe(take(4))

// Using concatAll()
const flattenedObs = sourceObs.pipe(
  map(innerObsFactory),
  concatAll(),
);

// Using concatMap(...)
const flattenedObs = sourceObs.pipe(
  concatMap(innerObsFactory)
)
```
_All other flattening operators follow the same logic._


### `mergeAll()` / `mergeMap(...)`

`mergeMap(...)` = `map(...)` + `mergeAll()`

[`mergeAll()`](https://rxjs.dev/api/operators/mergeAll) emits the values coming from all inner observables, as soon as they are emitted; values from inner observables can be **interleaved**.


### `switchAll()` / `switchMap()`

`switchMap(...)` = `map(...)` + `switchAll()`

[`switchAll()`](https://rxjs.dev/api/operators/switchAll) emits value produced by **the most recent** inner Observable.

As soon as the source observable emits a new inner observable, `switchAll()` unsubscribes from the previously subscribed inner observable and subscribes to the new inner observable.


### `exhaustAll()` / `exhaustMap(...)`

`exhaustMap(...)` = `map(...)` + `exhaustAll()`

[`exhaustAll()`](https://rxjs.dev/api/operators/exhaustAll):
- subscribes to the first inner observable
- emits the values emitted by the inner observable being currently subscribed to until it completes (_is exhausted_)
- during that time, **ignores** all inner observables that are emitted by the source observable
- after the currently subscribed inner observable completes, waits until a new inner observable is emitted
- repeats the process

