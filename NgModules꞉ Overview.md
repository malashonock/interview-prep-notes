---
tags: [Angular/modules]
title: 'NgModules: Overview'
created: '2023-09-18T12:02:43.105Z'
modified: '2023-09-18T13:50:57.888Z'
---

# NgModules: Overview

An NG module is a class decorated with `@NgModule`.


## Role

NG modules' role:
- configure respective `ModuleInjector`s
- NG compiler
- help organize related things together

When a template is being compiled, the compiler does directive matching using:
- selectors of directives in own `declarations` array
- selectors of directives _exported_ from NG modules and standalone directives in the `imports` array


## `@NgModule` API

### `declarations`

An array of _declarables_ that belong to this NG module and can be used by other declarables within this NG module.

**Declarables**:
- components
- directives
- pipes

**NB!** Services are not declarables.

**NB!** 1 declarable must belong to only 1 NG module.

**NB!** Standalone declarables cannot be included in `@NgModule.declarables` array.


### `imports`

An array of:
- other NG modules whose `exports` need to be made available within this NG module
- standalone declarables that need to be made available within this NG module


### `exports`

An array of:
- _declarables_ that can be used by any NG module or standalone c/d that `imports` this NG module
- _other NG modules_ whose `exports` need to be made available to any importer of this NG module (_without the need to explicitly import the re-exported module_)


### `providers`

An array of `@Injectable`s (services, etc.) that will be available for injection both within this NG module and **to the entire graph of importers** of this NG module, up to the **`root`** module (or a **lazy-loaded** root module) &mdash; providers' imports are flattened!

`@Injectable`s with `providedIn` specified should not be included in NG module `providers`.

An `@Injectable` can be neither included in this NG module `providers` nor have `providedIn` &mdash; in that case it must be made available by explicitly specifying it in the consumer's `providers` array.


### `bootstrap`

For an NG module driven app - **an array** of root component(s) that should be rendered first when bootstrapping the app.

When there are more than 1 bootstrap component specified, each of the should have its own host element in the `index.html`.


## Frequently used NG modules

- `BrowserModule`: to run the app.
- `CommontModule`: to use `ngIf`, `ngFor` etc.
- `FormsModule`: to enable template-driven forms (that use `NgModel`)
- `ReactiveFormsModule`: to enable reactive forms
- `RouterModule`: to set up routing and to use `routerLink` etc.
- `HttpClientModule`: to make HTTP requests

**NB!** `BrowserModule` already re-exports `CommonModule`.

**NB!** HTTP interceptors must be provided in the same module that imports `HttpClientModule`.
















