---
tags: [Angular/CD]
title: 'CD: Strategies'
created: '2023-09-18T15:07:13.887Z'
modified: '2023-09-19T06:43:59.094Z'
---

# CD: Strategies


## CD overview

**Change detection** = running the component's template fn in the Update mode.

As CD runs on the component, it:
- traverses each expression in its template
- calculates the new value of the binding
- compares the actual value with the previous one
- updates the DOM, if the value has changed

**NB!** Regardless of the strategy, NG calls `tick()` whenever the async APIs patched by Zone.js are triggered.  

During a CD cycle, NG attempts to traverse the entire view hierarchy, top to bottom.

For each view, NG checks its CD strategy and determines whether or not this view should be checked.

If a view is skipped from CD, the entire subtree of components beneath it is skipped as well.

The difference between strategies is only in what component (sub)trees are affected by each `tick()`.


## Default CD strategy

Whenever the recursive `detectChanges()` stemming from a `tick()` reaches a component with **default** CD strategy, NG:
- runs CD on this component
- moves on to its child views.


## OnPush CD strategy

CD is run on a component with **OnPush** only if:
- component's `@Input`s change (via `===` check)
- NG handles any event (`[event]` binding, `@Output` binding, `@HostListener`) in the component _or any of its children, regardless of their CD strategy_
- we triggered CD manually

**NB!** **Mutating** objects assigned to `@Inputs` will _not_ trigger CD:
- either run CD manually
- or use immutable structures, i.e. instead of mutating, clone objects

**NB!** NG detects changes in `@Input`s only via templates &mdash; modifying `@Input` values _in TS class_ must be accompanied with triggering a CD cycle **manually**.

**NB!** Observables per se don't trigger CD &mdash; it's the **async** pipe that, when applied, does the following under the hood:
- handles unsubscriptions
- calls `markForCheck()`, i.e. triggers CD


## Triggering CD manually

- inject `ChangeDetectorRef` (see separate note) and call either of:
  - `markForCheck()` (preferred)
  - `detectChanges()`
- inject `ApplicationRef` and call `tick()`: this will trigger a CD cycle for the entire app



