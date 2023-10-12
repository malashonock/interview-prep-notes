---
tags: [Angular/components]
title: 'NG: Components: Styling'
created: '2023-10-12T09:33:45.224Z'
modified: '2023-10-12T09:49:19.971Z'
---

# NG: Components: Styling

[Angular docs](https://angular.io/guide/component-styles)


## Best practices

- use CSS custom properties
- use global `@mixin`s
- (for `ViewEncapsulation.ShadowDom`) use `::part` pseudo-element


## NG-specific CSS selectors


### `:host` / `:host(...)`

Selects the component's host element.

In function form, additional selectors can be specified that must be applied to the host element.

**Example**: given an NG component with selector `some-component`, `:host(.active)` selector will look for a `<some-element class="active">`.


### `:host-context(...)`

Exists only in function form.

Looks for the selectors passed within `(...)` **above** the host element, up to the document root.

**Example**: `:host-context(.active)` will look for any DOM ancestor of this component's host element with class `active` applied.


## `::ng-deep`

`::ng-deep` pseudo-class **disables view encapsulation** of the component for that CSS rule.

**NB!** Be sure to include `:host` selector before `::ng-deep` to scope the specified rule to the current component and its children &mdash; bare `::ng-deep` will bleed into the global scope.
