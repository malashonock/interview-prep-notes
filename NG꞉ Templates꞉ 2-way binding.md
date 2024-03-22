---
tags: [Angular/templates]
title: 'NG: Templates: 2-way binding'
created: '2023-10-10T13:08:58.504Z'
modified: '2024-03-22T08:47:40.237Z'
---

# NG: Templates: 2-way binding

**Syntax**: `[()]` (a.k.a. _banana in a box_)

2-way binding = property binding + event binding:
- a parent component class field is property-bound to child component `@Input` (parent -> child)
- whenever child component emits a change event, `$event` value is assigned to parent component class field (child -> parent)

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

