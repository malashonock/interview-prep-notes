---
tags: [Angular/router]
title: 'Router: Named outlets'
created: '2023-09-11T14:26:53.565Z'
modified: '2023-09-11T15:18:46.599Z'
---

# Router: Named outlets

Named outlets are the targets for _secondary routes_.  
**Use case**: usually used to render popups/modals, etc.

How secondary routes look in address bar:
```
<!-- 1 secondary path -->
http://base-path/primary-route-path(aux-outlet-name:aux-route-path)

<!-- The same as above -->
http://base-path/(primary:primary-route-path//aux-outlet-name:aux-route-path)
```

**NB!** Secondary routes are enclosed in `(...)` and separated with `//`.


## Definition of a named outlet:

In a template:
```
<!-- Default outlet -->
<router-outlet></router-outlet>

<!-- Named outlet -->
<router-outlet name="..."></router-outlet>
```

**NB!** If outlet name is not specified, its name is `primary` by default.


## Definition of a secondary route

```
{
  path: 'aux-route-path',
  component: SomeComponent,
  outlet: 'aux-outlet-name',
}
```


## Navigating to a secondary route

```
<a 
  [routerLink]="[{ 
    outlets: { 
      'aux-outlet-name': 'aux-route-path' 
    }
  }]"
>Click me</a>
```

**NB!** Each auxiliary outlet has _its own navigation_, not affected by primary outlet navigations. _E.g. if a popup is rendered at an auxiliary outlet, navigations in the primary outlet won't affect or close the popup_.


## Removing a secondary route

```
this.router.navigate([{
  outlets: {
    'aux-outlet-name': null // <---
  }
}], {
  relativeTo: this.route.parent // <---
})
```

**NB!** The parent of the `ActivatedRoute` is specified to remove the auxiliary outlet from its containing segment.


