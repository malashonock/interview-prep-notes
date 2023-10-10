---
tags: [Angular/templates]
title: 'NG: Templates: Template input variables'
created: '2023-10-10T14:00:27.859Z'
modified: '2023-10-10T14:07:58.621Z'
---

# NG: Templates: Template input variables

**Use cases**:
- explicitly on `<ng-template>`s
- implicitly in structural directives

**Syntax**: `let-templateVarName="directiveVarName"`, where:
- `templateVarName` is some identifier used within the `<ng-template>`
- `directiveVarName` is some field of the directive applied to the `<ng-template>`

**NB!** If no value is assigned, such TIV will refer to the `$implicit` value of the directive.

Example:
```
// *ngFor unwraps to the following:
<ng-template
  ngFor
  [ngForOf]="heroes"
  let-hero // $implicit, i.e. a hero of heroes
  let-i="index"
>
  <li>Hero #{{i}}: {{hero.name}}</li>
</ng-template>
```
