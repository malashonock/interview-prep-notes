---
tags: [Angular/router]
title: 'NG: Router: Accessing URL information'
created: '2023-09-11T12:34:44.978Z'
modified: '2023-10-25T11:26:10.334Z'
---

# NG: Router: Accessing URL information

## Pre-Angular 16

[ActivatedRoute](https://angular.io/api/router/ActivatedRoute)
[ActivatedRouteSnapshot](https://angular.io/api/router/ActivatedRouteSnapshot)

1. Inject an `ActivatedRoute` instance
2. If value **changes** need to be tracked, use observable props from the `ActivatedRoute` instance
3. If a simple **snapshot** as of running the `ngOnInit` is needed, use namesake non-observable props from the `ActivatedRoute#snapshot`

Props of interest are:
- `paramMap`: for URL params (or less convenient `params`)
- `queryParamMap`: for URL search params (or less convenient `queryParams`)
- `fragment`: for hash
- `data`: for data passed via `resolve` guard

**Use case for observables**: `Router` re-uses component instances on repeated navigations between same component paths. Re-rendering a component if only `id` param has change is expensive &mdash; subscribe to param changes instead (usually, via `switchMap`).

**Use case for snapshot**: if you can't repeatedly navigate to various paths using the same component, simply use the route snapshot.

**NB!** [In most cases](https://angular.io/guide/router-tutorial-toh#observable-parammap-and-component-reuse), unsubscribing from subscriptions to `ActivatedRoute` observables is not needed, `Router` will handle them on destroy. 

**NB!** `ActivatedRoute` observables are never completed, `complete`/`finalize` will not be run.


## Angular 16+

Automatically binds URL data to routed components' `@Input`s.

Applicable to:
- URL params
- search params
- fragment
- static `data`
- resolver data

Configuration:
- **NgModule-based**: add `bindComponentsToInputs: true` to `RouterModule.forRoot(...)`.
- **Standalone-based**: `provideRouter(...)` + `withComponentInputsBinding()` arg.

**NB!**: Applicable only to components bound to `Route#component`.


