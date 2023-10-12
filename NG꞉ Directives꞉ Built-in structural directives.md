---
tags: [Angular/directives]
title: 'NG: Directives: Built-in structural directives'
created: '2023-10-10T14:36:00.272Z'
modified: '2023-10-11T09:29:40.627Z'
---

# NG: Directives: Built-in structural directives


## `NgIf`

[NgIf API](https://angular.io/api/common/NgIf)

Directive props:
- `ngIf`: the condition:
  - if truthy, adds the child component to the DOM
  - if falsy, removes it from the DOM and disposes of the component and its subcomponents
- `ngIfThen`: content to render if condition is truthy:
  - if omitted, the component hosting the directive will be rendered
  - if specified, the hosting component will be ignored
- `ngIfElse`: content to render if condition is falsy

Micro-syntax using `*ngIf`:
```
<some-element
  *ngIf="
    condition [as cnd]; // <-- can be aliased
    else #elseTpl // <--- TRV to a template
  "
>
  This will be rendered if {{cnd}} is true
</some-element>

<ng-template
  #elseTpl
  let-cnd // <--- to make local variable available
>
  This will be rendered if {{cnd}} is false
</ng-template>
```


## `NgFor`

[NgFor API](https://angular.io/api/common/NgFor)

Directive props:
- `ngForOf`: an iterable to loop over
- `ngForTrackBy`: a fn to compute identity of each item:

**NB!** `trackBy` should always be specified to avoid:
- excessive DOM teardown and re-creation
- animation errors

Micro-syntax using `*ngFor`:
```
<some-element
  *ngFor="
    let item // <--- $implicit
    of items;
    let i = index; // <--- a local var "i" bound to directive's var "index"
    // i as index <--- does the same
    trackBy: someTrackByFn
  "
>
  Each {{item.content}} will be rendered at index #{{i}}
</some-element>
```

Available local variables:
- `$implicit`: an item in the iterable
- `index`
- `count`
- `first` / `last`
- `even` / `odd`


## `NgSwitch`

`NgSwitch` is a set of 3 directives:
- `NgSwitch`: **attribute** directive
- `NgSwitchCase`: **structural**
- `NgSwitchDefault`: **structural**

Usage:
1. Apply `[ngSwitch]="condition"` to the containing element
2. Within the container:
  - apply `*ngSwitchCase="caseValue"` for each case element
  - apply `*ngSwitchDefault` for the default element

Logic:
- Every matching case element is rendered
- If no cases matched, the default element is rendered
- **NB!** Elements within `[ngSwitch]`, but not marked with either `*ngSwitchCase` or `*ngSwitchDefault` will be rendered!

**NB!** Unlike JS `switch..case` that uses strict equality (`===`), NG uses **loose equality** (`==`) for case matching!

