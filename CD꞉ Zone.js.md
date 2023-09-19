---
tags: [Angular/CD]
title: 'CD: Zone.js'
created: '2023-09-18T11:59:55.955Z'
modified: '2023-09-19T07:24:23.557Z'
---

# CD: Zone.js


## Zone.js overview

A `Zone` is an _execution context that survives multiple JS VM turns_.

"Execution context" = _this_.  
Without `Zone`s, _this_ is lost in async fn calls.  
Zone.js provides async fns with its own _"zoneThis"_ (`Zone#current`).

`Zone`s allow to keep track of _async_ fn calls.  
`Zone`s can be hierarchical.

Zone.js allows to **hook into the lifecycle of async tasks** (using `onXXXTask` hooks), and to take some actions based on it.

`Zone` task types:
- macro tasks (`setTimeout()` and `setInterval()`)
- micro tasks (promises)
- event tasks (`addEventListener()`)

`Zone` task hooks:
- `onScheduleTask`: when a new async task is scheduled (e.g. when `setTimeout()` is called)
- `onInvokeTask`: when a scheduled task is about to run (e.g. when a callback passed to `setTimeout()` is about to run)
- `onInvoke`: when a _sync_ fn is about to run in the zone
- `onHasTask`: whenever a task type changes status between `stable` (=no tasks) and `unstable` (=a new task is scheduled)

Zone.js monkey-patches many async APIs, e.g.:
- all browser events
- `setTimeout()` and `setInterval()`
- promises
- XHR requests
- Websocket messages
- some other (see [list of patched APIs](https://github.com/angular/angular/blob/HEAD/packages/zone.js/STANDARD-APIS.md))

**NB!** Some APIs are not patched - beware! E.g.:
 - `ResizeObserver` API (without a plugin)

**Use cases**:
- long descriptive stack traces
- **Angular change detection**


## Zone.js in Angular

Angular provides a singleton `NgZone` service.  

`NgZone`:
- keeps reference to the parent `zone`
- **creates a child `zone`** in which Angular runs
- hooks into zone lifecycle and **triggers change detection**:
  - hooks into `onHasTask` and calls `checkStable()`
  - inside `checkStable()`, emits an `onMicroTaskEmpty` event
  - `ApplicationRef` subscribes to `onMicroTaskEmpty` event and calls `tick()` method
  - `tick` calls **`detectChanges()`** on `ApplicationRef`'s view trees, recursively.

**NB!** `ChangeDetectorRef` is an abstract class, concrete class `ViewRef` inherits from it.


## `NgZone` API

`NgZone` exposes public methods:
  - **`run`**: to run a fn inside `NgZone` (_for APIs not patched by zone.js_)
  - **`runOutsideAngular`**: to run a fn outside `NgZone` (_for frequently running tasks that shouldn't lead to view changes_)

Use cases for `runOutsideAngular`:
- `requestAnimationFrame`
- `setTimeout` / `setInterval`
- macro or micro tasks stemming from 3rd party libs


## Zone.js configuration

NG CLI imports `zone.js` automatically:
- previously, explicitly in `polyfills.ts` file
- nowadays, in `angular.json`: `architect.build.options.polyfills` option

### Disable selected APIs patching

You need to create file `zone-flags.ts` and import it into `polyfills.ts` **before** importing `zone.js`.

To disable patching `requestAnimationFrame`:
```
(window as any).__Zone_disable_requestAnimationFrame = true;
```

To run certain DOM events outside `NgZone`, e.g. `scroll` and `mousemove`:
```
(window as any).__zone_symbol_UNPATCHED_EVENTS = [
  'scroll', 
  'mousemove'
];
```

### Disable `Zone.js` at all

1. Remove `zone.js` imports from `polyfills.ts`
2. Bootstrap NG app with the `noop` zone:
```
platformBrowserDynamic()
  .bootstrapModule(
    AppModule, 
    { ngZone: 'noop' } // <---
  )
```




