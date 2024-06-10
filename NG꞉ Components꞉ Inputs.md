---
tags: [Angular/components, Angular/inputs, Angular/v16+]
title: 'NG: Components: Inputs'
created: '2024-01-05T15:03:28.155Z'
modified: '2024-06-10T15:26:58.024Z'
---

# NG: Components: Inputs

[Angular.dev](https://angular.dev/guide/components/inputs)

Input = a component class field that is made bindable via the component template by applying the `@Input()` decorator to it.

Features:
- NG recognizes inputs statically, i.e. at compile time; inputs can't be added or removed at runtime.
- base component's inputs are inherited by derived components.
- input names are case-sensitive

## Required inputs

Since NG v16, an `@Input` can be made required as follows:
```
@Input({ required: true }) inputName!: InputType;
```

With this setting, omission of such input in a template that uses this component will generate a compile-time error.


## Input transforms

To apply a transformation to a value set in the template on its way to becoming the value of a component class field, pass a fn to the `transform` option:
```
// ...Elsewhere in code:
const fallbackToEmptyString = (value: string | undefined): string => {
  return value ?? '';
};

// ...In component class:
@Input({ transform: fallbackToEmptyString }) label = '';
```

**NB!** Input transform fns must be statically analyzable, e.g. no conditional branching, etc.

**NB!** Input transform fns must be **pure**.

NG has a few **built-in transform fns**:
- `booleanAttribute`: simulates HTML boolean attributes, i.e.:
  - `attributeName` (no value)
  - `attributeName=""`
  - `attributeName="attributeName"`
- `numberAttribute`: tries parsing to a number:
  - returns the number, if parsed successfully
  - returns `NaN` otherwise


## Input aliases

To set the name of the input in the template:
```
@Input({ alias: 'nameInTheTemplate' }) nameInComponentClass = 0;
```


## Inputs with getters/setters

`@Input()` decorator can be applied to:
- a getter/setter pair (set once before the getter)
- a read-only getter
- a write-only setter

**NB!** Avoid using getter/setter inputs &mdash; NG runs functions used in the template, including getters, on every CD cycle, which can cause performance issues.


## Monitoring input value changes

To track input value changes, use the `ngOnChanges` LCH.
