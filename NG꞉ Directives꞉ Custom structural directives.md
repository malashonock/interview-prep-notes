---
tags: [Angular/directives]
title: 'NG: Directives: Custom structural directives'
created: '2023-10-11T12:01:22.473Z'
modified: '2023-10-11T12:44:54.204Z'
---

# NG: Directives: Custom structural directives


## Structural directive shorthand

`*` prefix is usually used with structural directives.

This convention is automatically unwrapped by NG into a longer form:
```
// NgFor: shorthand
<div
  *ngFor="
    let user
    of users$ | async as users;
    let i = index; // <-- declaring local var
    let isOdd = odd; // <-- declaring local var
    trackBy: trackById
  "
  [class.odd]="isOdd" // <-- using local var
> 
  #{{ i + 1 }}: {{ user.name }}
</div>

// NgFor: longer form
<ng-template
  ngFor
  [ngForOf]="users$ | async"
  let-user // <-- =$implicit
  let-users="ngForOf"
  let-i="index"
  let-isOdd="odd"
  [ngForTrackBy]="trackById"
>
  <div [class.odd]="isOdd">
    #{{ i + 1 }}: {{ user.name }}
  </div>
</ng-template>
```

**NB!**: There can be only **1 structural directive per component**!


## Creating custom structural directives

Recipes:
- inject `TemplateRef` to get reference to the `<ng-template>`
- inject `ViewContainerRef`
- to render template content, use `this.viewContainerRef.createEmbeddedView(this.templateRef)`
- to remove template content, use `this.viewContainer.clear()`


## Micro-syntax

[Structural directive syntax reference](https://angular.io/guide/structural-directives#structural-directive-syntax-reference)  
[Good tutorial](https://dev.to/this-is-angular/mastering-angular-structural-directives-micro-syntax-demystified-340l)

Micro-syntax RegExp:      
`*:prefix="( :let | :expression ) (';' | ',')? ( :let | :as | :keyExp )*"`, where:
- `:prefix`:
  - the `exportAs` name of the directive, to be used in the template (e.g. `ngFor`)
  - the prefix which will be prepended for any `:key` in the micro-syntax (e.g. `ngFor` + `of` = `ngForOf` property)
- `:key`: short key in the microsyntax (e.g. `of ...`)
- `:expression`: any NG expression
- `:let` expression RegExp: `let :local = :export (;)?`
- `:as` expression RegExp: `:export as :local (;)?`
- `:keyExp` RegExp: `:key (:)? :expression (as :local)? (;)?`
- `:local`: local variable used in the **template**
- `:export`: internal variable exported **from the directive**

How NG translates shorthand:
- `:prefix` -> `prefix`:
```
// Shorthand...
*ngFor="..."

// ...translates to:
ngFor ...
```
- `:prefix=":expression"` -> `[prefix]="expression"`:
```
// Shorthand...
*ngIf="show"

// ...translates to:
[ngIf]="show"
```
- `:keyExp` -> `[prefixKey]="expression" let-prefixKey="export"`:
```
// Shorthand...
*ngFor="... of users$ | async as users"

// ...translates to:
ngFor 
[ngForOf]="users"
let-users="ngForOf"
...
```
- `:let` -> `let-local="export"`:
```
// Shorthand...
*ngFor="let user ...; let i = index"

// ...translates to:
ngFor
let-user // <-- same as let-user="$implicit"
let-i="index"
...
```


    
