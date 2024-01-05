---
tags: [Angular/templates, Angular/v17+]
title: 'NG: Templates: New control flow'
created: '2024-01-04T14:36:11.222Z'
modified: '2024-01-05T14:38:07.176Z'
---

# NG: Templates: New control flow

[angular.dev](https://angular.dev/guide/templates/control-flow)


## `@if`

[Angular University](https://blog.angular-university.io/angular-if/)

```
@if (a > b) {
  {{a}} is greater than {{b}}
} @else if (b > a) {
  {{a}} is less than {{b}}
} @else {
  {{a}} is equal to {{b}}
}
```

To alias the conditional expression result:
```
@if (users$ | async; as users) {
  {{ users.length }}
}
```


## `@for`

[Angular University](https://blog.angular-university.io/angular-for/)

```
@for (item of items; track item.id) {
  {{ item.name }}
}
```

**NB!** new `track` vs older `NgFor.trackByFn`:
  - now it's **required**
  - now it's an **expression** that returns a key (`track $index` is a good default)

`NgFor` contextual variables are available, as previously:
```
@for (item of items; track item.id; let idx = $index, e = $even) {
  Item #{{ idx }}: {{ item.name }}
}
```

**NB!** For cases when the collection is empty, `@empty` block can be used:
```
@for (item of items; track item.name) {
  <li> {{ item.name }} </li>
} @empty {
  <li> There are no items. </li>
}
```


## `@switch`

[Angular University](https://blog.angular-university.io/angular-switch/)

```
@switch (condition) {
  @case (caseA) {
    Case A.
  }
  @case (caseB) {
    Case B.
  }
  @default {
    Default case.
  }
}
```

Conditional expression is mathed via JS `===` semantics.

NG `@switch` doesn't fall through - no break or return is needed.

`@default` block is optional - if no `@case` matches, nothing is rendered.

