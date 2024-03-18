---
tags: [Angular/dynamic components]
title: 'NG: Dynamic components: Overview'
created: '2023-10-12T15:10:03.796Z'
modified: '2023-10-12T15:34:04.755Z'
---

# NG: Dynamic components: Overview

[Angular docs](https://angular.io/guide/dynamic-component-loader)  
[`ViewContainerRef` API](https://angular.io/api/core/ViewContainerRef)

In order to load new components at runtime:
1. Prepare the insertion point:
- create a simple component outlet directive:
  - inject `ViewContainerRef` in dir's Ctor
- in the host component template, place an `<ng-template>` element where new components will be placed, and apply the newly created component outlet directive to it
2. In the host component class, set up the dynamic component instantiation logic:
- gain access to the component outlet directive via `@ViewChild(ComponentOutletDirectiveClass)`
- gain access to the `ViewContainerRef` on the component outlet directive, e.g. `this.componentOutletDir.viewContainerRef`
- use `ViewContainerRef` APIs:
  - to create a component at runtime:
    - call `ViewContainerRef#createComponent(SomeComponentClass)` &mdash; this will return a `ComponentRef` instance
    - access the actual component class instance via `ComponentRef#instance`
    - set inputs on the created component instance
  - to remove the component, call `ViewContainerRef#clear()`
