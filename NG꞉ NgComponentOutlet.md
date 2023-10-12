---
tags: [Angular/dynamic components, Angular/v16+]
title: 'NG: NgComponentOutlet'
created: '2023-09-21T13:41:12.088Z'
modified: '2023-10-12T14:04:31.160Z'
---

# NG: [`NgComponentOutlet`](https://angular.io/api/common/NgComponentOutlet)

Allows **dynamic component creation** in a **declarative** way, without resorting to low-level concepts like `ComponentFactoryResolver`.


## `NgComponentOutlet` API

Technically, `NgComponentOutlet` is a directive with `ngComponentOutlet` selector, and the following `@Input`s:
- required:
  - `ngComponentOutlet` (=selector): a component class that should be rendered in the outlet
- optional:
  - `ngComponentOutletInputs`: `@Input`s that will be bound to the component
  - `ngComponentOutletInjector`: parent injector for the newly created component, defaults to the injector of the **current view container**
  - `ngComponentOutletContent`: array of arrays of root nodes to project into **`<ng-content>`** of the component being created
  - `ngComponentOutletNgModule`: an `NgModule` to load dynamically, which will load the component


Most often used in the form of a _structural directive_ with the following micro-syntax:
```
<ng-container
  *ngComponentOutlet="
    SomeComponentClass;
    inputs: inputsExpr;
    injector: injectorExpr;
    content: contentExpr;
    ngModule: ngModuleExpr;
  "
></ng-container>
```





