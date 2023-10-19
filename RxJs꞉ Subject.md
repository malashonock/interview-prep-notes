---
tags: [RxJs]
title: 'RxJs: Subject'
created: '2023-10-09T13:15:07.353Z'
modified: '2023-10-17T13:05:24.710Z'
---

# RxJs: Subject

[RxJs reference](https://rxjs.dev/guide/subject)

## `Subject`

- is an **observable**: can be `subscribe(...)`d to by many observers (=multicast)
- is an **observer**: has methods `next(...)`, `error(...)` and `complete(...)`, through which values can be multicasted to all subscribed observers

**Note**: Subjects are like EventEmitters: they maintain a registry of their listeners.


## `BehaviorSubject`

= `Subject`, plus:
- stores the latest emitted value
- whenever a new observer subscribes, it will receive this current value (even if it subscribed after this value had been emitted)


## `ReplaySubject`

= `BehaviorSubject`, plus:
- keeps `N` latest emitted values in buffer (`N` is specified during the construction)
- new observers receive the cached values upon subscription

**Tip**: for an observable to behave as a `ReplaySubject`, one can use [RxJs `shareReplay(n)`](https://rxjs.dev/api/operators/shareReplay) operator factory.


## `AsyncSubject`

- waits until `complete()`
- after that, emits the latest value to its observers
