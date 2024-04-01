---
tags: [JS/promises]
title: 'JS: Promises: Overview'
created: '2024-04-01T06:31:38.877Z'
modified: '2024-04-01T08:06:53.268Z'
---

# JS: Promises: Overview


## Order of execution


### Promise Ctor execution

Promise Ctor's _executor fn_ is run **synchronously**:
```
new Promise((resolve, reject) => {
  // Logs synchronously,
  // despite the fact that the promise will be resolved
  // in some subsequent VM cycle after 1000ms
  console.log(1);

  setTimeout(() => {
    resolve(2);
  }, 1000); 
})
```


### Side effects in args

If an expression that contains **side effects** is passed to:
- `Promise.resolve(...)`
- `Promise.reject(...)`
- `Promise#then(...)`
- `Promise#catch(...)`
- `Promise#finally(...)`  
the side effects within it are **evaluated synchronously**.

Example with `Promise.resolve()`:
```
Promise.resolve(1); // synchronously evaluates to a resolved promise fulfilled with 1
Promise.resolve(console.log(1)); // logs '1' synchronously and evaluates to a resolved promise fulfilled with undefined
```

Example with `Promise#then()`:
```
// Output by VM cycle:
// VM cycle #1: 2 (sync script execution)
// VM cycle #1: 3 (sync script execution)
// VM cycle #1: 1 (draining microtask queue)
Promise.resolve(1) /* 
  synchronously resolved promise fulfilled with 1 
*/
  .then(console.log(2)) /* 
    1. logs '2' synchronoulsy
    2. arg evaluates to undefined, i.e. not a fn
    3. non-fn arg is treated as passthrough identity fn: (value) => value
    4. immediately adds the identity fn to the microtask queue
    5. waits for the rest of the script to run to the end syncronously
  */
  .then(console.log); /*
    when the microtask added by previous .then() finishes executing,
    adds (value) => console.log(value) to the microtask queue
  */
console.log(3); /* executes synchronously before .then() callbacks */
```


### Macro vs. micro task queue

Calling **`setTimeout`** will postpone further promise resolution to some **subsequent** JS VM cycle.  
Callbacks passed to **`Promise#then()`**, unless they contain async code, are added to the microtask queue at the end of the **current** JS VM cycle.
```
// Output by VM cycle:
// VM cycle #1: 1 (sync script execution)
// VM cycle #2: 2 (from macrotask queue)
// VM cycle #2: 3 (from microtask queue)
// VM cycle #2: 4 (from microtask queue)
new Promise((resolve, reject) => {
  // Executes synchronously
  console.log(1);

  // Sync execution just requests the host env to schedule the callback
  // The host env will add the cb to the macrotask queue almost immediately (timeout is 0),
  // but it will be executed only in the next VM cycle
  // so will be resolved the promise
  setTimeout(() => {
    console.log(2);
    resolve(3);
  }); 
})
  .then((value) => {
    // This callback will be added to microtask queue once the original promise resolves,
    // i.e. during the 2nd VM cycle
    // The cb will be executed in the same 2nd VM cycle
    console.log(value);
  })
  .then(() => {
    // This callback will be added to microtask queue once the promise returned by preceding .then() fulfills with undefined,
    // i.e. during the same 2nd VM cycle
    console.log(4);
  });
```


### Sequence of microtask callbacks

**NB!** Callbacks passed to chained `Promise#then()`s are added to the microtask queue as their internally generated wrapper promises resolve, **not all at once**.

Example with interleaved `Promise#then()` callbacks from multiple promises:
```
// Output by VM cycle:
// VM cycle #1: 1 (sync script execution)
// VM cycle #1: 2 (sync script execution)
// VM cycle #1: 5 (sync script execution)
// VM cycle #1: 6 (sync script execution)
// VM cycle #1: 3 (from microtask queue, added by 1st promise's 1st then)
// VM cycle #1: 7 (from microtask queue, added by 2nd promise's 1st then)
// VM cycle #1: 4 (from microtask queue, added by 1st promise's 2nd then)
// VM cycle #1: 8 (from microtask queue, added by 2nd promise's 2nd then)
console.log(1);
Promise.resolve(console.log(2))
  .then(() => {
    // This one will be added to microtask queue synchronously
    // immediately after the 1st promise resolves,
    // but will not move to the downstream then
    // until the internally generated promise resolves
    console.log(3)
  })
  .then(() => {
    // This one will not be added until previous then's callback executes
    console.log(4);
  });
console.log(5);
Promise.resolve(console.log(6))
  .then(() => {
    // This one will be added to microtask queue synchronously
    // immediately after the 2nd promise resolves,
    // but will not move to the downstream then
    // until the internally generated promise resolves.
    console.log(7);
  })
  .then(() => {
    // This one will not be added until previous then's callback executes
    console.log(8);
  });
```





