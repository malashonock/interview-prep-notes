---
tags: [Angular/router]
title: 'Router: Guards'
created: '2023-09-11T15:19:36.928Z'
modified: '2023-09-12T08:11:46.336Z'
---

# Router: Guards

Guards control access to a route.
Guards are added to a `Route` configuration.

Guards are implemented as:
- a class implementing the respective interface with a single guard fn
- a standalone guard fn

Guard fn return options:
- `true` means _continue navigation_
- `false` means _cancel navigation and stay where you were_
- `UrlTree` will redirect to the specified URL

**NB!** All of the above can be async, i.e. wrapped in `Promise` or `Observable`. The `Router` will wait for the promise to resolve, or the observable to emit any value (after which it will complete automatically).


## List of guards

### `canActivate`

_Can I navigate to this `path`?_

Use cases: authentication, authorization.

### `canActivateChild`

_Can I navigate to any of the `children` of this `path`?_


### `canDeactivate`

_Can I navigate away from this `path`?_

Use cases: prompt for unsaved changes before leaving a form.

### `canMatch`

_Should I consider this `path` at all during URL matching?_

`canMatch` is run _before_ a module is lazy-loaded.

**NB!** If `canActivate` prevents navigation to a lazy-loaded route, the lazy module will be loaded anyway! However, if `canMatch` blocks navigation, the lazy module is not loaded.

For all guards except `canMatch`, returning `false` stops the entire navigation.  
However, when `canMatch` returns `false`, the `Router` moves on to the next route as if the route with `canMatch` didn't exist.

### `resolve`

_What data must be retrieved before navigating to this `path`?_

Used to delay rendering of the routed component until all necessary data have been fetched.

**NB!** If `resolve` fn returns an observable, it _must complete_; if it returns a promise, it _must resolve_. Otherwise the page will never load.

**Tip**: To show a loading spinner until the resolver resolves:
1. inject a `Router` instance
2. subscribe to `Router#events`
3. if `event instanceof NavigationStart`, set the loading status on
4. if `event instanceof NavigationEnd`, set the loading status off
5. wire up some template (e.g. `mat-progress-bar`) to the loading status

To retrieve the data returned by the resolver from within the component, subscribe to `ActivatedRoute#data` or use `ActivatedRoute#snapshot.data`.

**NB!** Returning an empty observable from resolver cancels navigation.


## Guard execution order

1. `canDeactivate`, bottom (children) to top (parent)
2. `canActivate` + `canActivateChild`, top to bottom















