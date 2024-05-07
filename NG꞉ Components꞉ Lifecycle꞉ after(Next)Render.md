---
tags: [Angular/components]
title: 'NG: Components: Lifecycle: after(Next)Render'
created: '2024-03-22T12:13:33.996Z'
modified: '2024-03-22T13:00:21.330Z'
---

# NG: Components: Lifecycle: after(Next)Render

## Rationale

**Task:** need to use browser APIs to manually read or modify the **DOM**.

**Problems**: 
- traditional LCHs run on all platforms, both in CSR as well during SSR and SSG
- no browser APIs are available in SSR and SSG
- to run the required DOM-related logic inside LCHs, one has to resort to conditional branching using `ifPlatformBrowser()`

**Solution**: `afterNextRender` / `afterRender` LCHs that can be used unconditionally &mdash; they won't run on server, but will be triggered on the next CD cycle in browser.


## Overview

[Angular.dev](https://angular.dev/guide/components/lifecycle#afterrender-and-afternextrender)

`afterNextRender` / `afterRender` LCHs differ from traditional LCHs.  
They are _not_ class methods &mdash; they are **standalone functions** that must be called in an _injection context_ (typically, in a Ctor).

**Tip**: to run `afterNextRender` / `afterRender` inside class methods, an instance of `Injector` can be injected and passed within the options object.

Traditional LCHs are flushed on a component-by-component / view-by-view basis &mash; `afterNextRender` / `afterRender` are called after NG has finished rendering _all components_ on the page into the DOM.

Both hooks have the same signature:
```
after(Next)Render(
  renderCallback: VoidFunction, 
  options?: AfterRenderOptions
): AfterRenderRef
```

## `afterNextRender`

[afterNextRender API](https://angular.io/api/core/afterNextRender)

Registers a callback to be run **once** after the next change detection cycle.

**Use cases**: 
- one-off initialization, e.g.:
  - initializing 3rd party libs
  - setting up `MutationObserver` / `ResizeObserver` / `IntersectionObserver` and other browser APIs
- observe discrete changes to DOM, e.g.:
  - element creation
  - element deletion

Example:
```
constructor() {
  afterNextRender(() => {
    this.chart = new MyChart(this.chartRef.nativeElement);
  }, {
    phase: AfterRenderPhase.Write
  });
}
```


## `afterRender`

[afterRender API](https://angular.io/api/core/afterRender)

Registers a callback to be run after **every** change detection cycle that follows.

**Use-cases**:
- observe frequent changes to DOM
- keep in sync with the DOM
- need access to DOM geometry, e.g. `getBoundingClientRect()`


## `AfterRenderOptions`

[AfterRenderOptions API](https://angular.io/api/core/AfterRenderOptions)

- (optional) `injector` (if invoked outside of class Ctor or class field initializer)
- (optional) `phase`: the `AfterRenderPhase` the render callback should be invoked in; defaults to `AfterRenderPhase.MixedReadWrite` &mdash; use a more specific phase instead!

### `AfterRenderPhase`

[AfterRenderPhase API](https://angular.io/api/core/AfterRenderPhase)

Available enum options (**in the order they are called**):
- `EarlyRead`:
  - used to read from the DOM before another render callback registered with `Write` phase (e.g. for positioning a tooltip)
  - can degrade performance, use `Read` or `Write` if possible
- `Write`: 
  - used for render callbacks that only write to DOM
  - never use it for callbacks that read from the DOM
- `MixedReadWrite`:
  - **default** phase
  - can degrade performance, avoid whenever possible (e.g. split into `EarlyRead` and `Write` callbacks)
- `Read`:
  - used for render callbacks that read from the DOM
  - never use it for callbacks that write to DOM

**NB!**: Inside each phase, render callbacks are invoked in the order they were registered.

