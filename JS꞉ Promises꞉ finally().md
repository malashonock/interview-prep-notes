---
tags: [JS/promises]
title: 'JS: Promises: finally()'
created: '2024-04-01T08:49:58.917Z'
modified: '2024-04-01T09:16:54.608Z'
---

# JS: Promises: `finally()`

`Promise#finally(onFinallyCallback)` is semantically close to `Promise#then(onFinallyCallback, onFinallyCallback)`, with the following differences:
- `onFinallyCallback` is NOT passed any args, i.e. its signature is `() => any`
- if `onFinallyCallback` is **synchronous**:
  - if it returns normally, the return value is **discarded**, i.e. upstream promise result is NOT modified
  ```
  // Output: 1
  Promise.resolve(1)
    .finally(() => 2)
    .then(console.log);
  ```
  - if it _throws an error_, the upstream promise becomes **rejected** with the error thrown inside `onFinallyCallback`
  ```
  // Output: 5
  Promise.resolve(1)
    .finally(() => { throw 3; })
    .then((value) => value * 2) /* this will NOT run */
    .catch((error) => error + 2) /* this WILL be run */
    .then(console.log);
  ```
- if `onFinallyCallback` is **asynchronous**:
  - the upstream promise resolution falls down to further chained handlers without any modification
  - `onFinallyCallback` spawns own flow of async execution that may finish normally or throw an exception &mdash; but it will not affect the original promise
  ```
  // Output:
  // 1
  // 3
  // (...in 1 second) 2
  Promise.resolve(console.log(1))
    .finally(() => {
      setTimeout(() => console.log(2), 1000);
    })
    .then(() => console.log(3));

  // Output:
  // 1
  // 3
  // (...in 1 second) Uncaught 2
  Promise.resolve(console.log(1))
    .finally(() => {
      setTimeout(() => { throw 2; }, 1000);
    })
    .then(() => console.log(3));
  ```
