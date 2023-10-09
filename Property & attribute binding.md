---
tags: [Angular/bindings]
title: Property & attribute binding
created: '2023-10-09T14:08:03.529Z'
modified: '2023-10-09T14:32:05.319Z'
---

# Property & attribute binding

[Attributes and properties](https://javascript.info/dom-attributes-and-properties)


## Property binding

Example:
```
<img alt="photo" [src]="photoUrl">
```
The brackets `[]` make NG evaluate the RH side of the assignment as a _dynamic expression_ (e.g. `[src]="photoUrl"`).

Without the brackets, NG treats the RH side as a _string literal_ and assigns it verbatim (e.g. `alt="photo"`).

**NB!**: `[colSpan]="..."` (property) &mdash; but `[attr.colspan]="..."` (attribute).

**Use cases**:
- toggle button features
- set paths programmatically
- share values between components


## Attribute binding

Almost the same as property binding, but attribute name must be prefixed with `attr.`, e.g.:
```
<button ... [attr.aria-label]="some-value">...</button>
```

**NB!**: If the RH expression resolves to `null` or `undefined`, NG **removes** the attribute at all.

**Use cases**:
- a11y
- styles
- CSS classes


## Class binding


### Single class binding

**Usage**: `[class.name]="expression"`
- if `expression` evaluates to `true`, NG applies the class `name`
- if `expression` evaluates to `false`, NG removes the class `name`


### Multiple class binding

**Usage**: `[class]="classExpression"`, where `classExpression` should return:
- a space-delimited string of CSS classes
- an array of CSS classes
- an object with CSS classes as keys and truthy/falsy expressions as values

**NB!**: If an array or object is used, for NG to update the CSS classes, its **reference must change** &mdash; mutating the array/object won't work.


## Style binding


### Single style binding

**Usage**:
- `[style.css-property-name]="expression"` (kebab-case)
- `[style.cssPropertyName]="expression"` (lowerCamelCase)

`expression` should return a valid CSS string.

**Tip**: You can specify unit extension, e.g. `px`, `rem` or `%`, and bind to a number, e.g.:
```
[style.width.px]="someNumber"
```


### Multiple style binding

**Usage**: `[style]="styleExpression"`, where `styleExpression` should return:
- a valid CSS style string
- an object with style names as keys and string style values as values

**NB!**: If an object is used, for NG to update the styles, its **reference must change** &mdash; mutating the object won't work.
