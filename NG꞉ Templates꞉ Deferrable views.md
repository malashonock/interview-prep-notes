---
tags: [Angular/templates, Angular/v17+]
title: 'NG: Templates: Deferrable views'
created: '2024-01-05T06:46:59.867Z'
modified: '2024-01-05T14:32:46.979Z'
---

# NG: Templates: Deferrable views

[angular.dev](https://angular.dev/guide/defer)
[Angular University](https://blog.angular-university.io/angular-defer/)
[Angular Experts](https://angularexperts.io/blog/angular-defer-lazy-loading-total-guide)
[Netanel Basal](https://netbasal.com/a-comprehensive-guide-to-angulars-defer-block-468c74048df4)

**Use case**: deferrable views (DV) can be used **to lazy-load parts of a template**. Lazy-loading of entire templates is normally done via lazy-loaded routes.


## Dependencies

**Dependencies** in the context of DV are C/D/Ps + associated assets (images, css, etc.).


**NB!** DV dependencies do _not_ relate (directly) to data fetching requests to a backend API. DV is _not_ a "loading spinner" facility for long-running data fetches &mdash; rather, it's a tool for lazy-loading parts of UI _at initial rendering of a component/page_.


## Blocks

Building blocks:
- `@defer`: main block, required, lazy-loaded
- `@placeholder`: optional (but highly recommended), eagerly loaded
- `@loading`: optional, eagerly loaded
- `@error`: optional, eagerly loaded

Usage:
```
@defer (<triggers>) {
  <lazy-loaded-main-cmp />
} @placeholder (minimum <...s/ms>) {
  <eagerly-loaded-placeholder-cmp />
} @loading (after <...s/ms>; minimum <...s/ms>) {
  <eagerly-loaded-loading-indicator-cmp />
} @error {
  <eagerly-loaded-error-msg-cmp />
}
```


### `@defer`

The only lazy-loaded block.

C/D/Ps ("dependencies") inside `@defer` block will be compiled to separate JS chunks, apart from the `main.js` bundle, and lazy-loaded according to `prefetch` trigger.

**NB!** Only **standalone** C/D/Ps will actually be deferred!  
`NgModule`-based dependencies included in `@defer` block will be loaded eagerly (included in the main chunk/bundle).

**NB!** Still, `@defer` itself can be used in both standalone and `NgModule`-based C/D/Ps.

`@defer` block's contents will not be rendered until after `@defer` trigger kicks off rendering of DV


### `@placeholder`

Contains content to show before `@defer` trigger kicks off rendering of DV, _except the period when dependencies are being lazy-loaded_.

**NB!** Deps inside `@placeholder` are loaded **eagerly**.

**Params:**
- `minimum`: min. amount of time placeholder template must be shown (to prevent flickering on fast starting loads); _if loading starts earlier, placeholder template will still be rendered until `minimum` has lapsed_.

**Note**: Hereinafter &mdash; all time intervals can be specified in seconds (`s`) or milliseconds (`ms`).


### `@loading`

Contains content to show during the loading of deferred dependencies (JS chunks), _but only if `@defer` trigger has already fired_.

**NB!** Deps inside `@loading` are loaded **eagerly**.

**Params:**
- `after`: min. amount of time to wait since loading has begun before showing the loading template; _if loading finishes earlier, loading template will not be rendered_.
- `minimum`: min. amount of time loading template must be shown (to prevent flickering of fast finishing loads); _if loading finishes earlier, loading template will still be rendered until `minimum` has lapsed_.


### `@error`

Contains content to show in case deferred loading fails.

**NB!** Deps inside `@error` are loaded **eagerly**.

**Params:** none


## Triggers

Are specified inside the `@defer` block:
```
@defer (
  on <trigger>;
  when <condition>;
  prefetch on <trigger>;
  prefetch when <trigger>
) {
  ...
}
```

**NB!** Multiple triggers can be combined via `;` &mdash; this is always a logical **OR** combination.

**NB!** Various types of triggers can be combined.


### Trigger types by type of condition

- `on` triggers: pre-defined, declarative
- `when` triggers: user-defined, imperative


#### `on` triggers

- `on idle` = when the browser reaches an idle state (as detected via `requestIdleCallback` API)
  - **default** value for `@defer` trigger
- `on immediate` = ASAP, not even waiting for the browser to become idle
- `on viewport` / `on viewport(target)` = when `target` enters the viewport (as detected via `IntersectionObserver` API)
- `on hover` / `on hover(target)` = when `target` catches any of the following events:
  - `mouseenter`
  - `focusin`
- `on interaction` / `on interaction(target)` = when `target` catches any of the following events:
  - `click`
  - `keydown`
- `on timer(...)` = no sooner than specified time

**NB!** **`target`** can be any of:
- a TRV, specified explicitly
- if `target` is not specified, `target` = placeholder template


#### `when` triggers

Can specify any valid JS expression/component class field.

Once the condition becomes **truthy**, deferred loading is triggered.


### Trigger types by stage of loading lifecycle

- main `@defer` trigger (without any prefixes): configures when DV should be rendered (or start fetching their code chunks, if need be)
- `prefetch` trigger: configures when deferred code chunks pre-fetching should start
  - only has impact if fires earlier than the `@defer` trigger

