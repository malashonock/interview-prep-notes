---
tags: [Angular/DI]
title: 'NG: DI: Injecting deps'
created: '2023-09-12T09:09:00.808Z'
modified: '2023-10-04T13:37:13.968Z'
---

# NG: DI: Injecting deps

Injecting = requesting a dep from NG DI system. 


## Deps injection options

1. Inside a class **Ctor**
2. Using the global `inject()` fn


## Injection context

- Ctor of a class instantiated by NG DI, e.g. `@Injectable` or `@Component`
- Factory fn used in `Provider.useFactory` or passed to `InjectionToken` Ctor
- In a fn run in injection context:
  - NG-defined, e.g. router's `CanActivateFn`
  - user-forced via `runInInjectionContext()` fn

**NB!** `inject()` fn can only be run in an injection context.
