---
tags: [Angular/content projection]
title: 'NG: Content projection: NgTemplateOutlet'
created: '2023-09-05T08:23:36.275Z'
modified: '2023-10-12T15:08:20.586Z'
---

# NG: Content projection: NgTemplateOutlet

[Stephen Cooper talk @AngularConnect](https://www.youtube.com/watch?v=2SnVxPeJdwE)  
[My version with structural directives](https://stackblitz.com/edit/ngtemplateoutletcontext-xvzh2j?file=src%2Fapp%2Fmy-selector%2Fmy-selector.component.html)  
[Angular docs](https://angular.io/api/common/NgTemplateOutlet)

`NgTemplateOutlet` is a structural directive that inserts an embedded view from a prepared `TemplateRef`.

**Use case** &mdash; to facilitate the customization of a component by delegating template slots definition to the _consumers_ of the component.

**NB!** `<ng-content>` sometimes is enough &mdash; but not always, e.g. in `*ngFor` loops:
- will render only once
- will render with incorrect context


## `NgTemplateOutlet` API

Available props:
- `ngTemplateOutlet`: a `TemplateRef` from which an embedded view will be created
- `ngTemplateOutletContext` (optionally): a context object with exported variables that can be used as local variables in the `TemplateRef`
- `ngTemplateOutletInjector` (optionally): the injector to be used in the created embedded view

With micro-syntax:
```
<some-element
  *ngTemplateOutlet="
    templateRef;
    context: { ... };
    injector: ...
  "
>
</some-element>
```


## Usage


### Component being customized

In the component template:
```
<some-container-component>

  <!-- That's where content will be projected -->
  <ng-container 
    *ngTemplateOutlet="
      templateRef; 
      context: { $implicit: ..., index: ... }
    "
  >
  </ng-container>

</some-container-component>
```

Any element can host the directive, not only `<ng-container>`.

`templateRef` is the reference to the template that will be rendered on that place (can be a `#templateVariable` or a class field of type `TemplateRef<...>`)


In the component class:

- Simple case, without a custom structural directive:
```
@ContentChild('templateRef', { static: false }) templateRef: TemplateRef<...>;
```
**NB!** Template name should be provided by the parent correctly!

- More complex, if content is projected using a custom structural directive:
```
@ContentChild(MyStructuralDirective)
directive: MyStructuralDirective;

get templateRef(): TemplateRef<any> {
  return this.directive.templateRef;
}
```
**NB!** Injection in Ctor won't work - probably because it's applied to a child?


### Consuming component

In the component template:

- Simpler case, without structural micro-syntax:
```
<customized-component>
  <ng-template 
    #templateRef 
    let-implicitVarName 
    let-index="index"
  >
    <div>
      Some content {{ implicitVarName }} at {{ index }}
    </div>
  </ng-template>
</customized-component>
```

- More complex, with a custom structural directive:
```
<customized-component>
  <div
    *myStructuralDirective="
      let implicitVarName;
      let index = index
    "
  >
    Some content {{ implicitVarName }} at {{ index }}
  </div>
</customized-component>
```


### (Optionally) Custom structural directive:

```
@Directive({
  selector: [myStructuralDirective],
})
export class MyStructuralDirective {
  constructor(public templateRef: TemplateRef<any>) { }
}
```

`templateRef` must be public to be accessible from the customized component.

Basically, no other logic is needed &mdash; structural directive is used only to leverage the micro-syntax.







