---
tags: [Angular/DI]
title: 'DI: forRoot() pattern'
created: '2023-09-18T13:32:36.283Z'
modified: '2023-09-18T13:48:29.486Z'
---

# DI: forRoot() pattern

This pattern uses static `forRoot()` and optionally `forChild()`/`forFeature()` methods to **separate providers from a module**.

Typical `forRoot()` implementation:
```
@NgModule({ ... })
export class SomeModule {
  static forRoot(/*args*/): ModuleWithProviders<SomeModule> {
    return {
      ngModule: SomeModule,
      providers: [
        // array of providers
      ]
    }
  }
}
```

## Use cases

Common example &mdash; **`RouterModule`**:
- exports commonly used directives `RouterOutlet` and `RouterLink`
- provides `Router` service
- root module calls `forRoot(...)` and receives a `Router`
- feature modules call `forChild(...)` and **don't** create new `Router` instances

Another use case for `forRoot(...)` &mdash; **passing arguments** to an NG module, e.g. environment variables etc.


## Prevent re-import

To guard against a lazy-loaded module re-importing this module:
```
constructor(@Optional() @SkipSelf() parentModule?: SomeModule) {
  if (parentModule) {
    throw new Error('SomeModule is already instantiated');
  }
}
```

