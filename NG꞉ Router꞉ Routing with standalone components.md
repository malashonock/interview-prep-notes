---
tags: [Angular/router, Angular/standalone]
title: 'NG: Router: Routing with standalone components'
created: '2023-10-16T08:09:23.152Z'
modified: '2023-10-16T08:51:23.929Z'
---

# NG: Router: Routing with standalone components

[Angular Architects](https://www.angulararchitects.io/en/blog/routing-and-lazy-loading-with-standalone-components/)
[Angular docs](https://angular.io/guide/standalone-components#routing-and-lazy-loading)


## 1. Provide services for root scope

In `main.ts`, `provideRouter()`:
```
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(
      APP_ROUTES,
      withPreloading(PreloadAllModules),
      withDebugTracing(),
    ),
  ],
})
```

**Note**:
  - modern libraries support the `provide***()` / `with***()` pattern for standalone apps
  - for legacy libraries where only `@NgModule`s are available, use `importProvidersFrom(<ngModule>)`

## 2. Import router directives

In a component that uses `<router-outlet>` or `[routerLink]`:
```
@Component({
  imports: [
    // RouterModule, <--- this will work as well

    // But better import what you need:
    RouterOutlet,
    RouterLinkWithHref,
  ]
})
```

## 3. Lazy loading standalone components

**Option 1**: Lazy load many routes at once using `loadChildren()` (recommended):
```
export const APP_ROUTES: Route[] = [
  {
    path: 'child-path',
    loadChildren: () => 
      import('./path/to/child-routes)
        .then(m => m.CHILD_ROUTES),
  },
];
```

**NB!**: This only works when every route loaded this way routes to a **standalone component**.

**Tip**: When using `default` export, `then()` can be skipped:
```
// In child-routes.ts
export default [
  { path: 'sub-route-1', component: StandaloneComponent1' },
  { path: 'sub-route-2', component: StandaloneComponent2' },
] as Route[];

// In main app:
export const APP_ROUTES: Route[] = [
  {
    path: 'child-path',
    loadChildren: () => import('./path/to/child-routes),
  },
];
```


**Option 2**: Lazy load a standalone component using `loadComponent()`:
```
export const APP_ROUTES = [
  {
    path: 'child-path',
    loadComponent: () => 
      import('./path/to/child.component)
        .then(m => m.ChildComponent),
  },
];
```

## 3. Providing services to specific routes

Add `providers` array to a specific route:
```
export const APP_ROUTES: Route[] = [
  {
    path: 'some-path',
    providers: [
      // These will be scoped to /some-path/*
    ],
  },
  // Other paths will not have access to the above providers
];
```

**NB!**: It's recommended to use `providedIn: ...` whenever possible for tree-shaking.

**Note**: Using route-scoped `providers` decouples lazy-loading from introducing additional injection scopes. Adding `providers` to a route **defines a new injection scope** (=a new environment injector), regardless of the route being lazy or not.
