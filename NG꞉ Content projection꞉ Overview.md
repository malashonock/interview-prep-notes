---
tags: [Angular/content projection]
title: 'NG: Content projection: Overview'
created: '2023-10-12T14:06:07.755Z'
modified: '2023-10-12T15:06:51.268Z'
---

# NG: Content projection: Overview

[Angular docs](https://angular.io/guide/content-projection#multi-slot-content-projection)


## Single-slot CP

**Usage**: 
- in the _projectee_ component template, place an `<ng-content>` element where projected content must appear
- in the _projector_ component template, place actual content between the opening and closing tags of the child _projectee_ component, e.g.:
```
<projectee-cmp>
  // Projected content is placed here
<projectee-cmp>
```

**NB!** `<ng-content>` element is just a placeholder next to which content will be projected, it doesn't create a real DOM element.


## Multi-slot CP

**Usage**:
- in the _projectee_ component template, assign a **CSS selector string** to each `<ng-content select="...">` property, e.g.:
```
...
  <ng-content select="[slot-1]"></ng-content>
  <ng-content select=".slot-2"></ng-content>
  <ng-content>
    Unmatched selectors will go here
  </ng-content>
...
```
- in the _projector_ component template, mark the respective elements placed within the _projectee_ component to match the selectors, e.g.:
```
<projectee-cmp>
  <p slot-1>
    This will go to slot 1
  </p>
  <p class="slot-2">
    This will go to slot 2
  </p>
  <p slot-3>
    There is no such slot - this will go to the default slot
  </p>
  <p>
    This will go to the default slot as well
  </p>
<projectee-cmp>
```


## Conditional CP

Usage of `<ng-content>` is not recommended, because the _projector_ **always** instantiates the content it projects, even if the _projectee_ has an `<ng-content>` wrapped in `*ngIf` or even no `<ng-content>` at all!

Use `<ng-template>` with `ngTemplateOutlet` structural directive instead (see [NG: Content projection: NgTemplateOutlet](https://github.com/malashonock/interview-prep-notes/blob/main/NG%EA%9E%89%20Content%20projection%EA%9E%89%20NgTemplateOutlet.md) note).


## `ngProjectAs`

In complex environments, `ngProjectAs=[some-complex-css-selector]` can be used, e.g.:
```
<ng-container ngProjectAs=".foo:not(.bar)[slot-1]">
  <p>This will go to slot 1</p>
</ng-container>
```

