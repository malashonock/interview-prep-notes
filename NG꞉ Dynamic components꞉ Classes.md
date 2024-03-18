---
tags: [Angular/dynamic components]
title: 'NG: Dynamic components: Classes'
created: '2023-10-13T07:39:26.618Z'
modified: '2023-10-13T11:00:34.475Z'
---

# NG: Dynamic components: Classes


## [`ElementRef`](https://angular.io/api/core/ElementRef)

A thin wrapper around the native (DOM) element inside of a view.

Class properties:
- `nativeElement`

**Access**:
- default `read` type for `@ViewChild(<TRV>)` query
- inject in Ctor (for an attribute directive applied to any element)


## [`ViewRef`](https://angular.io/api/core/ViewRef)

Extends abstract class `ChangeDetectorRef`.  
`ChangeDetectorRef` provides CD-related methods.  
`ViewRef` adds a few destroy-related APIs on top.

**Access**: inject to Ctor an instance of `ChangeDetectorRef` &mdash; basically, when a `ChangeDetectorRef` dependency is injected, it's an instance of `ViewRef` that will actually be provided.

`ViewRef` represents a _host view_ of a regular NG component.


## [`EmbeddedViewRef`](https://angular.io/api/core/EmbeddedViewRef)

Represents an _embedded view_ created from a `TemplateRef`.

Extends `ViewRef` with props:
- `context`: a context object associated with the `TemplateRef` (it will be directive-specific and vary for different templates)
- `rootNodes`: an array of root DOM nodes within <ng-template>

**Note**: In NG sources, there is actually no distinction between `ViewRef` and `EmbeddedViewRef` &mdash; instead, one single class **`ViewRef$1`** is used that includes `context` and `rootNodes`. It's just that when a `ViewRef` is implied, `context` is `null` and `rootNodes` are `[]`, whereas for `EmbeddedViewRef` they have meaningful values.

**Access**:
- return from `TemplateRef#createEmbeddedView()`
- return from `ViewContainerRef#createEmbeddedView()`


## [`TemplateRef`](https://angular.io/api/core/TemplateRef)

Represents an embedded template from which embedded views can be created.

**Note**: A component's template is also a `TemplateRef` (an _outer_ template), as opposed to the `TemplateRef`s that refer to `<ng-template>`s within the component's template (_inner_ templates).

Members of `TemplateRef` class:
- `elementRef`: an `ElementRef` that references the underlying `<ng-template>` DOM **comment** node
- `createEmbeddedView(context, injector?)`: a method to create an `EmbeddedViewRef` from this `TemplateRef`

**Access**:
- TRV (in the outer template)
- `@ViewChild(<TRV>)` query (in component class)
- inject in Ctor (for a structural directive applied to an `<ng-template>`)


## [`ComponentRef`](https://angular.io/api/core/ComponentRef)

Represents a component created by a component factory.

Class properties:
- `location`: an `ElementRef` to the host DOM element of the component, e.g. `<app-root>` element for `AppComponent`
- `injector`
- `instance`: instance of the component class:
  - **NB!** make sure you initialize `instance` `@Input()`s when creating components dynamically
- `hostView`: a host `ViewRef` created for this component
- `changeDetectorRef`: points to the _same_ `ViewRef` as `hostView`
- `componentType`: reference to the component class

Class methods:
- `setInput()`
- `destroy()` / `onDestroy()`

**Access**:
- return from `ComponentFactory#create()`
- return from `ViewContainerRef#createComponent()`


## Using [`ComponentFactory`](https://angular.io/api/core/ComponentFactory) (deprecated)

To dynamically create a component using the component factory:
1. Inject `ComponentFactoryResolver` and `ApplicationRef` into Ctor
2. Get the component factory by calling `ComponentFactoryResolver#resolveComponentFactory()`
3. Create a `ComponentRef` via `ComponentFactory#create()`
4. Do the wiring:
- initialize `@Input()`s on the `ComponentRef#instance`
- attach `ComponentRef#hostView` via `ApplicationRef#attachView()`
- on destroy:
  - call `ApplicationRef#detachView()`, passing it `ComponentRef#hostView`
  - call `ComponentRef#destroy()`

**NB!** The above method is highly NOT recommended by NG &mdash; use `ViewContainerRef` whenever possible!


## [`ViewContainerRef`](https://angular.io/api/core/ViewContainerRef)

[Useful explanation](https://hackernoon.com/exploring-angular-dom-abstractions-80b3ebcfc02)

A utility class for manipulating templates and components dynamically.

**Access**: a `ViewContainerRef` can be attached to any component or DOM node:
- if **injected in component Ctor**, its `_hostLView` will be set to the parent LView, i.e.:
  - to the root LView &mdash; for the root `AppComponent`
  - to the LView of the parent component &mdash; for any child component
- if queried via **`ViewChild(<TRV>, { read: ViewContainerRef })`**, its `_hostLView` will be set to the LView of the current component

`ViewContainerRef` marks an insertion point _next to which_ embedded views will be rendered.

Class properties:
- `element`: a native `ElementRef` to which the `ViewContainerRef` is attached
- `injector`
- `length`: how many `ViewRef`s are attached to this `ViewContainerRef`

Class methods:
- **`createEmbeddedView(templateRef, context, ...)`**: wrapper for `templateRef.createEmbeddedView()`
- **`createComponent(componentClass, ...)`**: wrapper around `ComponentFactory#create()`
- `get(index)`: get a `ViewRef` from this `ViewContainerRef` at the given `index`
-  `indexOf(viewRef)`
- `insert(viewRef, index?)`
- `move(viewRef, targetIndex)`
- `remove(index?)`: remove a `ViewRef` at the given `index` (at the end, by default)
- `detach(index?)`: detach a `ViewRef` from this `ViewContainerRef` without destroying it (for re-use)

