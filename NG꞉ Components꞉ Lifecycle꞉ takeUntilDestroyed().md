---
tags: [Angular/components, Angular/v16+]
title: 'NG: Components: Lifecycle: takeUntilDestroyed()'
created: '2024-01-05T14:48:03.294Z'
modified: '2024-03-22T12:06:03.680Z'
---

# NG: Components: Lifecycle: takeUntilDestroyed()

Since NG v16, it's now possible to use **`takeUntilDestroyed()`** RxJs operator to unsubscribe from RxJs subscriptions when a component/directive/service is being destroyed.

Previously:
```
private destroy = new Subject();
...
someObservable.pipe(
  ...,
  takeUntil(this.destroy)
);
...
public ngOnDestroy(): void {
  this.destroy.next();
  this.destroy.complete();
}
```

New pattern:
```
someObservable.pipe(
  ...,
  takeUntilDestroyed()
);
```

Inside C/D/services, `takeUntilDestroyed()` doesn't need any args.

Inside an injection context, `takeUntilDestroyed()` can be called without args &mdash; implicitly, current `DestroyRef` will be used.
Outside of injection context, `takeUntilDestroyed()` must be explicitly passed an instance of `DestroyRef`.


## DestroyRef

[DestroyRef API](https://angular.io/api/core/DestroyRef)  

`DestroyRef` lets one set cleanup callbacks to run when the corresponding c/d/service is being destroyed.  
`DestroyRef` is injectable.  

### `onDestroy(callback)`

Signature:  
```
onDestroy(callback: () => void) => () => void;
```

Registers the given callback.  
Returns the fn that can be called to **unregister** the callback.

