---
tags: [Angular/DI]
title: 'DI: Providing deps'
created: '2023-09-12T09:06:42.860Z'
modified: '2023-09-12T09:10:36.520Z'
---

# DI: Providing deps

Providing = registering a dep with an injector.  

## In injectable class definition

```
@Injectable({ providedIn: ... })
```

**NB!** This way of providing deps, unlike the other, is tree-shakeable!

### `providedIn` options

#### `'root'`

Registers dep with the root injector.

Creates _a single instance_ of the dep that can be directly injected into _any_ class that needs it.

#### ~~NgModule (deprecated)~~

Registers dep with the `ModuleInjector` of the specified NgModule.

#### `null` or omitted

Dep is not provided automatically. Must be added to `providers` array of a `@NgModule`, `@Component` or `@Directive`.

#### `platform`

Registers dep with the platform injector.

##### ~~`any` (deprecated)~~

Provide one singleton instance of the dep across all eagerly-loaded modules, and create own instances for lazy-loaded modules.


## In `@NgModule.providers`

Dep will be available to:
- all d/c/p declared in this module
- all d/c/p declared in other modules within the same `ModuleInjector` applicable to this module

_The same instance_ of the dep is shared across all applicable consumers.


## In `@Component.providers` or `@Directive.providers`

Dep will be available to:
- all instances of this c/d
- other c/d's _used in the template of this component_

Each new instance of the component will receive _a new instance_ of dependency.

When a component instance is destroyed, so is the injected dep instance.


## In `@Component.viewProviders`

Dep will be available to consumers within the component `LView`.  
Dep will not be visible to content projected by the parent.


