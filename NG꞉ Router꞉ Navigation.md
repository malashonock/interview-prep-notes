---
tags: [Angular/router]
title: 'NG: Router: Navigation'
created: '2023-09-11T14:11:25.793Z'
modified: '2023-10-04T13:39:27.316Z'
---

# NG: Router: Navigation

## In template

Use native `<a>` tag with NG attributes:

### `routerLink`

**Accepts:**
- a string (_converted to an array of 1 string_)
- an array of strings (_commands_)
- `null`/`undefined` **disables the link**

Prefixing with `/` means an absolute path.  
No prefix or `./` prefix means _relative to the current URL_.   
`../` prefix means _up 1 level_, etc.

Passing URL params:
- required: simply specify value
- optional: as object, e.g. (`{ foo: 'bar' }` will be translated to `;foo=bar`)

### `queryParams`

**Accepts:** an object with URL search params.

### `fragment`

**Accepts:** a string with hash id.

### `routerLinkActive`

**Accepts:** the name of a CSS class that should be applied whenever the link is activated.


## In code

1. Inject a `Router` instance
2. Use `router.navigate(...)` method (preferred over `navigateByUrl(...)`)

[Read about `navigate` vs `navigateByUrl`](https://stackoverflow.com/a/45025432)

`navigateByUrl` creates a new URL.  
`navigate` patches the existing URL.

Navigating to a relative route:
```
// Assuming we are currently on /teams route,
// will navigate to /teams/11/player/33
this.router.navigate([11, 'player', 33], {
  relativeTo: this.route // ActivatedRoute
})
```

**NB!** Same prefix conventions (no prefix, `./`, `../`) apply to `navigate(...)`, but `relativeTo` prop should be set!

Other [`NavigationExtras`](https://angular.io/api/router/NavigationExtras) of interest:
- `queryParams`: add query params (URL search params)
- `fragment`: add fragment (hash)
- `skipLocationChange`: navigate silently, i.e. don't push new state on history stack
- `replaceUrl`: while navigating, replace current state in history, i.e. remove the current state and add new instead
