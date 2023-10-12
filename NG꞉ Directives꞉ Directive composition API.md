---
tags: [Angular/directives, Angular/standalone]
title: 'NG: Directives: Directive composition API'
created: '2023-10-11T12:45:59.186Z'
modified: '2023-10-11T12:55:52.337Z'
---

# NG: Directives: Directive composition API

[Directive composition API](https://angular.io/guide/directive-composition-api) allows to apply directive from inside a component/directive class (instead of component template).

**Usage**:
```
// Apply to a component
@Component({
  ...
  hostDirectives: [/* list directive classes here */],
})

// Apply to another directive
@Directive({
  ...
  hostDirectives: [/* list directive classes here */],
})
```

**NB!**: for a directive to be applied this way, it must be **standalone**. 

Features:
- host dir's host bindings are applied to the component's host element
- host dirs pass lifecycle steps **before** the classes/directives they are applied to
- host dir's `selector` is _ignored_ 
- host dir's inputs & outputs are NOT exposed as part of the component's API; expose explicitly as needed:
```
hostDirectives: [
  {
    directive: SomeDirective,
    inputs: ['inputName'], // <-- will be exposed
    outputs: ['eventName'], // <-- will be exposed
  }
]
```


