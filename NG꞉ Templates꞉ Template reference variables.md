---
tags: [Angular/templates]
title: 'NG: Templates: Template reference variables'
created: '2023-10-10T13:46:38.433Z'
modified: '2024-03-22T08:56:52.701Z'
---

# NG: Templates: Template reference variables

**Syntax**: apply `#someName` to a DOM element in the template.


## Referenced types

In case **no value is assigned** to the TRV, it refers to:
- if TRV is applied to an NG component: **component class instance**
- if TRV is applied to a native DOM element: **DOM element** (represented by `ElementRef`)
- if TRV is applied to a `<ng-template>`: **`TemplateRef`**

If **a name is assigned** to the TRV, e.g. `#userForm="ngForm"`, the TRV refers to a **directive or component** with a matching `exportAs` property (e.g. for `NgForm`, `exportAs` is `ngForm`).


## TRV scope

TRVs are scoped to the most narrow of:
- the scope of the **template** a TRV is declared in
- the scope of the containing `ng-template`, as well as structural directives (`*ngIf`, `*ngFor` etc.)
