---
tags: [Angular/router]
title: 'NG: Router: Setup'
created: '2023-09-11T08:31:24.932Z'
modified: '2023-10-04T13:39:33.760Z'
---

# NG: Router: Setup


## Eagerly-loaded modules
1. Create the _routing_ `@NgModule`
2. Define the `Routes` configuration array somewhere (usually, next to `@NgModule` in the same TS module)
2. **Import** `RouterModule` in the routing module, passing `routes` config to its static `forRoot` method
3. **Export** `RouterModule` from the routing module (to make Router directives available to the importing module without sharing the Router service)
4. Import routing module to the consuming `@NgModule`
5. Add `<router-outlet>`s to the consuming module templates as needed (content will be projected right after the tag, not replace it)


## Lazy-loaded modules

Additional steps:
a. in the parent routing module:
  1. in the respective route config, instead of `component`, specify `loadChildren: import('path-to-lazy-loaded-module').then((m) => m.ModuleName)`
b. in the lazy-loaded module:
  1. Create the routing module
  2. Configure nested `routes` subtree
  3. Import `RouterModule` to the routing module, passing `routes` to its static `forChild` method
  4. Export `RouterModule` from the routing module
  5. Import routing module to the consuming module

**NB!** When lazy-importing the module, use the consuming module, not its routing module!

`forRoot()` and `forChild()` are different in that they provide different services [see SO post](https://stackoverflow.com/a/56753173).


## Accessing URL information (**Angular 16**+)

- **NgModule-based**: add `bindComponentsToInputs: true` to `RouterModule.forRoot(...)`.
- **Standalone-based**: `provideRouter(...)` + `withComponentInputsBinding()` arg.


## Preloading strategy

In `RouterModule.forRoot(...)`, set the `preloadingStrategy` option:
- `NoPreloading` (=default)
- `PreloadAllModules`: preload _all_ modules in the background as soon as possible
- or provide a custom `PreloadingStrategy`

**Tip**: Example of a custom preloading strategy:
1. Say, we want to preload only those lazy routes that have a boolean `data.preload` set to `true`.
2. Implement a new `@Injectable` service that implements `PreloadingStrategy`'s `preload()` method.
3. If module should be loaded, call the load `fn` passed to `preload`; otherwise return `of(null)`.
4. Configure `RouterModule.forRoot(...)` to use the custom preloading strategy.

