---
tags: [Angular/components]
title: 'NG: Components: Component interaction patterns'
created: '2023-10-12T09:13:42.542Z'
modified: '2023-10-12T09:26:22.043Z'
---

# NG: Components: Component interaction patterns


## Parent to child


### Using child's `@Input()`s

A parent component can pass data down to its child component via setting the child's `@Input()`s.


## Child to parent


### Using child's `@Output()`s

A child component can pass data up to its parent component by emitting events from the child's `@Output()`s.


### Using TRVs in parent's template

1. In the parent's template, assign a TRV to the child component
2. By default, for a TRV applied to a custom element, NG will reference the component class instance
3. Elsewhere in the parent's template, use TRV to access the child components public methods and properties


### Using `@ViewChild()` in parent's component class

1. In the parent's component class, create a field of child component type.
2. Decorate it with `@ViewChild(ChildComponentClass)`
3. Use the reference safely in `ngAfterViewInit()` LCH


## Shared service

1. Provide a service in the parent component or higher upstream in the DI tree.
2. Use the service freely in both parent and child components

**NB!** Beware of modifying the parent's state by the child component indirectly via the service &mdash; NG will throw an [`ExpressionChangedAfterItHasBeenCheckedError`](https://angular.io/errors/NG0100) in dev mode.
