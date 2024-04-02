---
tags: [Angular/queries]
title: 'NG: Queries'
created: '2023-10-13T12:59:25.461Z'
modified: '2024-04-02T14:35:51.908Z'
---

# NG: Queries

Queries allow to gain access to DOM elements, components, directives, etc., declared in the component template, from within the component class. 

Queries are the recommended API for traversing the component hierarchy, as opposed to DI.

Queries overview:
Query | Where to search | What to return | Earliest LCH
--- | --- | --- | ---
[`ViewChild`](https://angular.io/api/core/ViewChild) | component's children | single result | `ngAfterViewInit`
[`ViewChildren`](https://angular.io/api/core/ViewChildren) | component's children | list of results | `ngAfterViewInit`
[`ContentChild`](https://angular.io/api/core/ContentChild) | projected content | single result | `ngAfterContentInit`
[`ContentChildren`](https://angular.io/api/core/ContentChildren) | projected content | list of results | `ngAfterContentInit`


## [`Query`](https://angular.io/api/core/Query) metadata

An abstract class (class interface) that defines the metadata that can be passed to a query:
- `selector`:
  - any class decorated with `@Component` or `@Directive`
  - TRV as a plain string
  - `TemplateRef` class (to query for an `<ng-template>`)
  - any provider class (e.g. a service) defined in the child component tree of the current component
  - any provider defined via a string token
  - **(`*Children` queries only)** a comma-separated string (for string-valued selectors)
- `read`: used to read a different token (other than the default one) from the queried elements:
  - `ElementRef` class (default for native DOM elements)
  - `TemplateRef` class (default for `<ng-template>`)
  - `ViewContainerRef` class
  - any class decorated with `@Component` (default for custom NG elements) or `@Directive`
  - any provider class (e.g. a service) defined in the child component tree of the current component
  - any provider defined via a string token
- `static`:
  - `true`:
    - to resolve query once the view is created, but **before** CD runs
    - usually used with native DOM elements in component template
    - query will be resolved in `ngOnInit` LCH (no need to wait until `ngAfterViewInit` / `ngAfterContentInit`)
    - value will be available **regardless** of binding resolution in structural directives like `ngIf`, `ngForOf`  
  - `false` (default):
    - to resolve query **after** CD runs
    - query will only be resolved in `ngAfterViewInit` / `ngAfterContentInit` LCHs
    - value will reflect changes in structural directives like `ngIf`, `ngForOf`
- `descendants` **(`Content*` queries only)**:
  - `true` (default): query all descendants of the element
  - `false`: only query direct (first-level) children of the element
- `emitDistinctChangesOnly` **(`*Children` queries only)**: **deprecated**, will always be `true` in future:
  - `true`: `QueryList#changes` will emit only if its result changes
  - `false`: `QueryList#changes` can emit even if its result hasn't changed

`Query` metadata support table:
`Query` metadata | ViewChild | ViewChildren | ContentChild | ContentChildren
--- | --- | --- | --- | ---
`selector` | + | + | + | +
`read` | + | + | + | +
`static` | + | + | + | +
`descendants` | - | - | + | +
`emitDistinctChangesOnly` | - | + | - | +


## `View*` vs. `Content*` queries

`View*` queries scan only among the elements used in a component's template (a.k.a. _view DOM_).

`Content*` queries scan among the elements projected into the current component (a.k.a. _content DOM_).


## `*Child` vs. `*Children` queries

`*Child`* queries return **the first** element matching the `selector`.  
Whenever view/content DOM changes and a new element matches the `selector`, the property is **updated**.

`*Children` queries return an iterable `QueryList`.  
Whenever an element is added to, moved in, or removed from, the view/content DOM, the `QueryList#changes` observable emits a new value (see `emitDistinctChangesOnly` config).



## [`QueryList`](https://angular.io/api/core/QueryList)

Represents the data returned by `*Children` queries.

Features:
 - **unmodifiable** &mdash; NG updates the list on its own as DOM structure changes
 - implements the **iterable** protocol, i.e. can be traversed via ES6 `for..of` or `NgForOf`
 - exposes a `changes` observable that emits whenever the query result changes

 [`QueryList` API](https://angular.io/api/core/QueryList)






