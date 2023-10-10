---
tags: [Angular/templates]
title: 'NG: Templates: 2-way binding'
created: '2023-10-10T13:08:58.504Z'
modified: '2023-10-10T13:43:17.379Z'
---

# NG: Templates: 2-way binding

**Syntax**: `[()]` (a.k.a. _banana in a box_)

2-way binding = property binding + event binding:
- a host component class field is property-bound to child component template (host component -> child template)
- whenever child component emits a change event, `$event` value is assigned to host component class field

Example:
```
// With 2-way binding shorthand
<some-element 
  [(childProp)]="someClassField"
></some-element>

// Unwraps to the following
<some-element
  [childProp]="someClassField"
  (childPropChange)="someClassField = $event"
></some-element>
```

To enable 2-way `[(prop)]` binding:
- declare `@Input() prop`
- declare `@Output() propChange` (i.e. property name + `Change` suffix)

