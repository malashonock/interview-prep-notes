---
tags: [Angular/DI, Angular/standalone]
title: 'NG: DI: DI with standalone components'
created: '2023-10-16T08:52:08.164Z'
modified: '2023-10-16T09:29:09.817Z'
---

# NG: DI: DI with standalone components

[Angular docs](https://angular.io/guide/standalone-components#dependency-injection-and-injectors-hierarchy)


## Environment injectors

In place of module injectors, NG introduces a new concept, **environment injectors** that can work with standalone apps.

Environment injectors can be configured as follows:
- traditional ways:
  - in **`@NgModule.providers`**: in `@NgModule`-based apps
  - in **`@Injectable.provideIn`**: in both `@NgModule`-based and standalone apps
- new ways:
  - in the `providers` of **`bootstrapApplication()`** call: in standalone apps
  - in the `providers` of a **`Route`** configuration

**Tip**: to manually create an environment injector, call `createEnvironmentInjector([/* providers go here */])`.

**NB!** Environment injectors can execute **initialization logic** upon their creation &mdash; similar to `@NgModule` Ctors that get executed when a module injector is created:
```
// Add this to providers:
{
  provide: ENVIRONMENT_INITIALIZER,
  useValue: () => {
    // this code will run when the environment injector gets created
  }
}
```


## Standalone injectors

A **standalone injector** is created in some cases to ensure that the providers imported by a standalone component are isolated from the rest of the app.

Today, NG will create a standalone injector as a _child of the current environment injector_, in the following cases:
- for all **bootstrapped components**: their standalone injectors will be children of the root environment injector
- for **dynamically created standalone components**:
  - by the `Router`
  - by the `ViewContainerRef`


