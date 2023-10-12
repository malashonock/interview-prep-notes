---
tags: [Angular/components]
title: 'NG: Components: Lifecycle'
created: '2023-10-12T07:43:16.440Z'
modified: '2023-10-12T08:24:43.824Z'
---

# NG: Components: Lifecycle

[Angular docs](https://angular.io/guide/lifecycle-hooks)

Lifecycle consists of:
- instantiation
- change detection and updates
- destruction

NG provides **lifecycle hooks** to hook into key events in the lifecycle.

LCH are supported by:
- **components**: all hooks
- **directives**: all hooks
- services: **OnDestroy** only!

**Order** of LCH:
1. `ngOnChanges()`
2. `ngOnInit()`
3. `ngDoCheck()`
4. `ngAfterContentInit()`
5. `ngAfterContentChecked()`
6. `ngAfterViewInit()`
7. `ngAfterViewChecked()`
8. `ngOnDestroy()`

**Note**: Class **constructor** is called the very first, before all LCH.

**NB!** Class Ctor should NOT contain any data-heavy logic (e.g. subscriptions, data fetching, etc.) &mdash; ideally, only injected deps.


## `ngOnChanges()`

Called whenever any of the c/d `@Input()`s are set (for the 1st time) or reset (updated).

**NB!**: As long as there are no `@Input()`s or they don't change, `ngOnChanges()` is NOT flushed.

A `SimpleChanges` object is passed a an arg to the hook, each entry of which contains a `SimpleChange` object for a given `@Input()`.

[`SimpleChange`](https://angular.io/api/core/SimpleChange) props:
- `previousValue` / `currentValue`
- `firstChange: boolean` / `isFirstChange(): boolean`


## `ngOnInit()`

Called **once**, after c/d initialization and 1st setting of the `@Input()`s.

**NB!**: `ngOnInit()` is always flushed, even if there are no bound `@Input()`s.

**Use cases**:
- setting up subscriptions
- data fetching service calls


## `ngDoCheck()`

Called **on every CD run**.

**NB!**: Since `ngDoCheck()` runs on every CD run, it should contain as little and light logic as possible.

**Use cases**: to detect and act upon changes that NG won't detect on its own, e.g.:
- custom CD (too rare?)
- object `@Input()`s mutation (bad practice? leverage immutability)


## `ngAfterContentInit()` / `ngAfterContentChecked()`

Called whenever NG **projects external content** into the current component's view (or the view a directive is in).

`ngAfterContentInit()` is called _once_ after the 1st `ngDoCheck()`.

`ngAfterContentChecked()` is called every time, after `ngAfterContentInit()` and every subsequent `ngDoCheck()`.

**Use cases**: place here the logic that depends on `@ContentChild()` / `@ContentChildren()` queries.


## `ngAfterViewInit()` / `ngAfterViewChecked()`

Called whenever NG initializes / checks the current component's view (or the view a directive is in) _and all of its child views_.

`ngAfterViewInit()` is called _once_ after the 1st `ngAfterContentChecked()`.

`ngAfterViewChecked()` is called every time, after `ngAfterViewInit()` and every subsequent `ngAfterContentChecked()`.

**Use cases**: place here the logic that depends on `@ViewChild()` / `@ViewChildren()` queries.


## `ngOnDestroy()`

Called immediately before NG destroys the d/c or service.

**Use cases**:
- unsubscribe from observables
- complete the destroy subject
- remove event listeners




