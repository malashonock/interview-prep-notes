---
tags: [Angular/signals]
title: 'NG: Signals: Effects'
created: '2024-01-04T13:32:25.457Z'
modified: '2024-01-04T14:16:50.865Z'
---

# NG: Signals: Effects

Features:
- an effect runs at least **once**
- when an effect runs, it tracks what underlying signals it reads; whenever any of the tracked signals change, the effect runs again
- effect **deps tracking is dynamic**
- effects run **asynchronously**, during CD


## Use cases for effects

Good use cases for effects:
- Logging/debugging purposes
- Syncing with `localStorage`
- Custom DOM manipulations that can't be expressed in template
- Custom rendering to a `<canvas>` or 3rd party charting/UI library

DON'Ts:
- don't use effects for propagation of state changes
- don't set signals inside effects, unless absolutely needed


## Creating an effect

Call **`effect(effectFn, options)`**.
```
effect(() => {
  console.log(`Current counter is: ${counter()}`);
});
```

`effectFn` signature: `(onCleanupFn) => void`

Possible `options`:
- `injector: Injector`: manually set injection context
- `manualCleanup: boolean`:
  - if `false` (default), will be cleaned up with the current `DestroyRef` (=c/d/service that created the effect)
  - if `true`, effect must be destroyed manually
- `allowSignalWrites: boolean`

**NB!** Effects can be created only in an **injection context**, i.e.:
- in a component c/d or service **Ctor**
- assigned to a c/d/service class **field** (TS type is `EffectRef`)
- outside Ctor (e.g. in a class method), an effect can be created by passing an `Injector` instance via its options:
```
readonly counter = signal(0);
constructor(private injector: Injector) {}

initializeLogging(): void {
  effect(() => {
    console.log(`The counter value is: ${this.counter()}`);
  }, {
    injector: this.injector
  });
}
```


## Destroying effects

In most cases, no dedicated action is needed - effects are destroyed when its enclosing context is destroyed (e.g. for effects created in component Ctor = when component is destroyed).

To destroy an effect **manually**, call `EffectRef.destroy()`.

To clean up resources when effect is destroyed, call the `onCleanup` fn inside the effect, passing it a callback fn specifying the cleanup actions:
```
effect((onCleanup) => {
  const timer = setTimeout(() = {
    console.log(`The counter value is: ${this.counter()}`);
  });
  
  onCleanup(() => {
    clearTimeout(timer);
  });
});
``` 

