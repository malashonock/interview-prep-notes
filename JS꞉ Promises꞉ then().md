---
tags: [JS/promises]
title: 'JS: Promises: then()'
created: '2024-04-01T08:07:28.732Z'
modified: '2024-04-01T08:33:15.621Z'
---

# JS: Promises: `then()`

`Promise#then(onFulfilledCallback, onRejectedCallback?)` inner logic unwraps roughly as follows:
```
(upstreamPromiseResult) => {
  return new Promise((resolve, reject) => {
    try {
      if (upstreamPromiseResult instanceof Error && onRejectedCallback) {
        const handledRejection = onRejectedCallback(upstreamPromiseResult);
        resolve(handledRejection);
      } else {
        const fulfilledResult = onFulfilledCallback(upstreamPromiseResult);
        resolve(fulfilledResult);
      }
    } catch (error) {
      reject(error);
    }
  });
}
```

The task of executing this logic is appended to the microtask queue exactly when (no sooner / no later than) _the upstream promise resolves_.

If `onFulfilledCallback` / `onRejectedCallback` are sync, the callbacks passed to chained downstream `then()`s will be appended to the microtask queue in the same JS VM cycle.


**NB!**: If passed a **non-function** arg, `then()`:
- evaluates the arg (running its possible side effects)
- behaves as if passed the **identity fn**, i.e. `value => value`
