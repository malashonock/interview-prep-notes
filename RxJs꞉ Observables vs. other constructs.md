---
tags: [RxJs]
title: 'RxJs: Observables vs. other constructs'
created: '2023-10-09T09:38:02.607Z'
modified: '2023-10-09T10:22:44.374Z'
---

# RxJs: Observables vs. other constructs

[Angular docs](https://angular.io/guide/comparing-observables)


## Comparison summary

Construct | Eager / Lazy | Push / Pull | # of values
--- | --- | --- | ---
`Function` | lazy | pull | single
`Iterator` | lazy | pull | multiple
`Promise` | eager | push | single
`Observable` | lazy | push | multiple

Pull vs Push:
- **Pull**:
  - Consumer decides when it receives data from Producer
  - Producer doesn't know when it will deliver the data to Consumer
- **Push**:
  - Producer decides when it sends data to Consumer
  - Consumer doesn't know when it will receive the data from Producer

## Observables vs. Promises

- Promise body (a.k.a. _executor fn_) executes immediately (eagerly); Observable execution doesn't start until subscription (lazily)
- Promises can provide only 1 value; Observables can provide any number of values in sequence (a stream of values)
- Promises do not differentiate between `then(...)` clauses (subscription + chaining); Observables differentiate between subscription (`subscribe(...)`) and chaining (`pipe(...)`)
- Promises are **non-cancellable**; Observables can cancel work on `unsubscribe()`


## Observables vs. events API

- Unlike `addEventListener(...)`/`removeEventListener(...)` API, Observers are **not** registered as listeners in the `Observable`
- An `Observable` doesn't keep track of attached observers
- Observables can transform the event before passing it to the handler


## Observables vs. arrays

- Arrays are synchronous, i.e. represent a static set of values, created all at once
- Observables are asynchronous, i.e. represent a stream of values over time

API mapping:
Operation | Observable | Array
--- | --- | ---
`concat(...)` | `concat(obs1, obs2)` | `arr1.concat(arr2)`
`forEach(...)` | `obs.pipe(tap(...))` | `arr.forEach(...)`
`map(...)`&#10;`filter(...)`&#10;`find(...)`&#10;`findIndex(...)`&#10;`reduce(...)` | `obs.pipe(<transformFn>(...))` | `arr.<transformFn>(...)`

