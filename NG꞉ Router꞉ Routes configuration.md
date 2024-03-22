---
tags: [Angular/router]
title: 'NG: Router: Routes configuration'
created: '2023-09-11T12:21:47.246Z'
modified: '2024-03-22T08:34:55.233Z'
---

# NG: Router: Routes configuration


## Route order

NG router uses **first-match wins** matching strategy &mdash; more specific routes should be placed above less specific ones in the `Routes` array.  
The catch-all (`**`) path should come last.

**Tip**: To debug route matching:
1. Inject a `Router` instance
2. Inspect `Router#config` prop


## `Route` configuration options

### `path`

Defines the **URL pathname segment** which, if matched, will trigger rendering the `component` or lazy-load the module specified in `loadChildren(...)`.

**Route params** are set using the `:` prefix (e.g. `:userId`)

path `**` is a catch-all path, it matches any URL segment. It's common to assign a 404 `component` to it.

### `pathMatch`

`prefix` _(=default)_

[See SO post.](https://stackoverflow.com/a/62476799)

Algorithm:
1. start from the root and move down to child paths
2. fully match this URL segment against this route `path` (`URL segment === route path`?)
3. if this path segment matches, _that's not the end!_ &mdash; move to the next segment and repeat step 2
4. route hit occurs only if **all** path segments have matched

`full`

Algorithm differences from above:
1. match this route `path` against this **and all remaining** (_unconsumed_) URL segments
2. route hit occurs if there are no remaining unmatched URL segments
3. if this route`path` doesn't match the remaining URL, **don't** move down to child paths

**Use case for `full`**: in redirects from empty-path routes, to prevent endless loops.

### `redirectTo`

A path to redirect to (from the path specified in `path` prop of this node).

**NB!** Common pattern for redirects from empty-path routes:
```
{
  path: '',
  pathMatch: full,
  redirectTo: 'some-other-path'
}
```

Redirects keep **route params** with same signature (e.g. `heroes/:heroId` to `superheroes/:heroId`).

Redirects handle **query params and fragments** as follows:
- when using _absolute_ redirects (starting with `/`), query params & fragment are taken from `redirectTo` route config
- when using _relative_ redirects, query params & fragment are taken from the source URL


### `matcher` (instead of `path` + `pathMatch`)

Specifies a custom [`UrlMatcher`](https://angular.io/api/router/UrlMatcher) fn for matching a route against a URL.

[Tutorial](https://angular.io/guide/routing-with-urlmatcher)

### `component`

Component that will be _eagerly_ rendered next to `<router-outlet>`.

### `loadChildren` (instead of `component`)

The dynamic import of a _lazy-loaded_ module with its own routing module and `routes`.

The callback fn can import either of:
- the `NgModule`
- the `Routes` configuration

### `loadComponent` (instead of `component`)

The dynamic import of a _lazy-loaded **standalone component**_.

### `children`

An **array** of child route definitions (=subsequent URL segments).

### `title`

Sets the document's title (=title of the browser tab) when the app is navigated to this URL.

Can be:
- a `string`
- an `@Injectable` implementing `Resolver` interface
- a `ResolveFn`

Additionally [custom title strategy](https://angular.io/guide/router#setting-the-page-title) can be provided.

### `outlet`

The `name` of `<router-outlet>` to which the bound `component` will be projected.

### `data`

Pass arbitrary data object to the routed component.


## Componentless routes

A common pattern is to wrap route subsets under a single parent route with empty (`''`) `path` and unspecified `component`, and add some common functionality to it (e.g. guards, etc.).

