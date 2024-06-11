---
tags: [Angular/signals, Angular/v17+]
title: 'NG: Signals: Writable signals'
created: '2024-01-04T13:07:33.596Z'
modified: '2024-06-11T07:22:32.024Z'
---

# NG: Signals: Writable signals

TS type: `WritableSignal<T>`


## Creating a writable signal

Call **`signal(...)`** fn, passing it the signal's **initial value**.
```
const counter: WritableSignal<number> = signal(0);
```


## Reading a writable signal's value

`signal()` returns a getter fn, calling which returns the current value of the signal.
```
console.log(`The counter is ${counter()}`);
```


## Updating a writable signal's value

2 ways:

1. use **`set(...)`** if there is no need to know the current value.
```
counter.set(3);
```

2. use **`update(fn)`** to derive a new value from the current one.
```
counter.update(value => value + 1);
```

