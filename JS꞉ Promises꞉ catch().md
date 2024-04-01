---
tags: [JS/promises]
title: 'JS: Promises: catch()'
created: '2024-04-01T08:32:08.902Z'
modified: '2024-04-01T08:47:34.906Z'
---

# JS: Promises: `catch()`

**NB!** `Promise#catch(onRejectedCallback)` is just a shorthand for `Promise#then(undefined, onRejectedCallback)`.

**NB!** `Promise#catch()` **falls through**:
 - if it catches the upstream error properly, the result is a **fulfilled** promise, handleable by `onFulfilledCallback`s in chained downstream `Promise#then()`s
```
// Outputs: 4
Promise.reject() /* 
  rejects with undefined 
*/
  .then(() => 1) /* 
    will not be run, since no onRejectedCallback is passed 
  */
  .catch(() => 3) /* 
    handles error and returns a new promise fulfilled with 3
  */
  .then((value) => ++value) /*
    this WILL run, since the upstream promise is fulfilled
  */
  .catch((value) => --value) /*
    this will NOT run
  */
  .then(console.log) /*
    logs the result
  */;
```
 - if it results in the same or another error, the result is a **rejected** promise, handleable by `onRejectedCallback`s in chained downstream `Promise#then()`s or `Promise#catch()`es


