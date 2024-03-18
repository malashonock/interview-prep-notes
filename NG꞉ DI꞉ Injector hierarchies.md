---
tags: [Angular/DI]
title: 'NG: DI: Injector hierarchies'
created: '2024-03-17T17:14:11.997Z'
modified: '2024-03-17T21:39:47.634Z'
---

# NG: DI: Injector hierarchies

Injector = intermediary b/w deps providers and deps consumers.

There are 2 types of injector hierarchies:
- **environment** hierarchy
- **element** hierarchy


### Element injectors hierarchy

[Alexey Zuev about NodeInjector](https://medium.com/angular-in-depth/angular-di-getting-to-know-the-ivy-nodeinjector-33b815642a8e)

Represented by [**`NodeInjector`** class](https://github.com/angular/angular/blob/f50afb10c37f19133053e2ea56c9b404e15916be/packages/core/src/render3/di.ts#L752) (implements `Injector` interface).

`NodeInjector` is part of a component LView/TView.data and is implemented via bloom filters. Given only components can have LViews, a `NodeInjector` is always associated with a component.

Created implicitly for every DOM element with an NG directive applied.


### Environment injectors hierarchy

Represented by [**`R3Injector`** class](https://github.com/angular/angular/blob/f50afb10c37f19133053e2ea56c9b404e15916be/packages/core/src/di/r3_injector.ts#L135) (extends abstract `EnvironmentInjector` class which implements `Injector` interface)

Environment injectors are configured via:
- `@Injectable` decorator &mdash; for all types of apps
- `@NgModule.providers` &mdash; for `NgModule`-based apps
- `providers` array in `ApplicationConfig` (in `main.ts` -> `bootstrapApplication` options)
- `providers` array in `Route` configuration

Module injectors hierarchy:
1. **Null injector**: if DI reaches it, NG error is thrown
2. **Platform injector**: created by `platformBrowserDynamic()` fn (called in `main.ts`) as configured by `PlatformModule`
3. **root injector**: created by:
 - `bootstrapModule(AppModule)` call in `main.ts` &mdash; in `NgModule`-based apps
 - `bootstrapApplication(AppComponent)` call in `main.ts` &mdash; in standalone apps
4. **child injectors**: (optionally) created during the instantiation of lazy-loaded routes
5. **standalone injectors**: created for bootstrapped, dynamically created or lazy routed standalone components

**NB!** In `NgModule`-based apps, root injector is a _flattening_ of `@NgModule.providers` arrays of all the modules that can be reached via `@NgModule.imports` of the root module. Child injectors are only for lazy-loaded modules, which are not available when root injector is created.

Use case for `PlatformInjector`: providing services shared by _microfrontends_.
