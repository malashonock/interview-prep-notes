---
tags: [Angular/DI]
title: Component tree navigation
created: '2023-09-05T09:30:01.862Z'
modified: '2023-09-07T07:16:49.942Z'
---

# Component tree navigation

[Navigate the component tree with DI](https://angular.io/guide/dependency-injection-navtree)

## View hierarchy

NG component instances don't have direct references to their parent or child components &mdash; the relation is indirect, i.e. via the component's **view** object.

Each component instance has a _host view_.  
A component instance can also have _embedded view(s)_.

Host view + embedded views comprise the view hierarchy for a given component.


## Navigation down the component tree

**Public API** is available:
- `ViewChild` / `ViewChildren` - for other components referenced in the components template
- `ContentChild` / `ContentChildren` - for other components projected into the component by its parent

**NB!** `Query` / `QueryList` are base classes for the above, respectively.


## Navigation up the component tree

**NB!** Not so straightforward &mdash; may be a sign of poor architecture!

All options that follow are based on **DI**.

### By concrete class

```
constructor(@Optional() public concreteParentInstance: ConcreteParent) { }
```

### By class interface

**NB!** It's not possible to simply inject an instance of a base class, without tricks with DI

**NB!** It's not possible to inject an instance of a TS interface &mdash; interfaces are removed after compilation to JS

**Class interface** = _abstract_ base class from which derived sub-classes inherit.

In the injectable class, it should be added to providers under the class interface token:
```
@Component({
  ...,
  providers: [{ 
    provide: AbstractBaseClass, 
    useExisting: forwardRef(() => ConcreteSubclass)
  }]
})
export const ConcreteSubclass { }
```

In the injecting class, the injected component should be typed as class interface:
```
constructor(
  @Optional()
  @SkipSelf()
  parentBaseSubclassInstance: AbstractBaseClass
) { }
```

**NB!** If the same class can be both an injectable and injecting, `SkipSelf()` should be specified to avoid circular reference error.


