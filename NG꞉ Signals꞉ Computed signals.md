---
tags: [Angular/signals, Angular/v17+]
title: 'NG: Signals: Computed signals'
created: '2024-01-04T13:08:30.653Z'
modified: '2024-06-11T07:21:32.154Z'
---

# NG: Signals: Computed signals

Computed signals derive their value from other signals.  
Whenever an underlying signal updates, the derived computed signal updates as well.

TS type: `Signal<T>`

Features:
- **lazy evaluation**: derivation fn is not run until the 1st time a computed signal is read.
- **memoization**: given no underlying signals change their value, subsequent reads of the computed signal are taken from cache; whenever an underlying signal updates, new computed value is calculated and cached.
- **computed signal deps tracking is dynamic**: if conditional branching hides any of the underlying signal deps, updating them won't trigger the update of the computed signal.


## Creating a computed signal

Call **`computed(fn)`** fn, passing it the fn that specifies how signal value should be derived from other signals.
```
const radius: WritableSignal<number> = signal(10);
const circleArea: Signal<number>  = computed(() => Math.PI * radius() ** 2);
```


## Reading a computed signal's value

`computed()` returns a getter fn, calling which returns the current value of the signal.
```
console.log(`The circle area is ${circleArea()}`);
```

