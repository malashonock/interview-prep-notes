---
tags: [Angular/CD]
title: 'CD: ChangeDetectorRef'
created: '2023-09-18T15:06:45.446Z'
modified: '2023-09-19T07:43:50.995Z'
---

# CD: ChangeDetectorRef

Base class that is used to intervene into the automatic CD.

`ChangeDetectorRef` is abstract, actual object that gets injected is instance of `ViewRef`, that inherits from `ChangeDetectorRef`.

## `ChangeDetectorRef` API


### `markForCheck()`

Used with `OnPush` CD strategy to mark the view as dirty and, by so doing, to _request_ NG to run CD on it once it's ready.

Features:
- asynchronous: CD is _not_ run rightaway, NG will gather all such "marks" and run CD during current or next `tick()`
- marks as dirty both the current component and **all its ancestors** up to the root view
- recommended approach


### `detectChanges()`

Force NG to run CD on this components subtree.

Features:
- synchronous: CD is run immediately
- CD runs only on this components and its **children**
- parent views are _not_ affected
- should be used when you're sure you need this method


### `detach()`

Exclude this component with all its children from automatic CD.

For detached views, CD can only be run via `detectChanges()`


### `reattach()`

Re-attaches the previously detached view to CD tree and re-enables automatic CD.


### `checkNoChanges()`

Checks the current view and all its children for changes, and **throws** if any changes have been detected.

