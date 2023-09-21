---
tags: [Angular/forms]
title: 'Forms: Validation - Overview'
created: '2023-09-19T15:26:50.172Z'
modified: '2023-09-20T13:06:09.548Z'
---

# Forms: Validation - Overview


## Native HTML validation

By default, NG disables native HTML form validation by implicitly adding `novalidate` attribute to the enclosing `<form>`, and uses NG validators instead (with namesake properties as in HTML validation).

To use native validation _together_ with NG validators, apply `ngNativeValidate` directive to the `<form>`.


## Foundation classes


### `Validator` interface

```
interface Validator {
  validate: ValidatorFn;
  registerOnValidatorChange?: (fn: VoidFunction) => void;
}
```

`validate` fn performs _synchronous_ validation against the provided control.

`registerOnValidatorChange` (_optional_) is fired whenever the validator inputs (=form control value) change.


### `ValidatorFn` interface

```
interface ValidatorFn {
  (control: AbstractControl): ValidationErrors | null;
}
```

`ValidatorFn` can be either part of `Validator` implementation, or a standalone validator fn used in reactive forms.


### [`Validators` class](https://angular.io/api/forms/Validators)

Provides a set of built-in validators, implemented as static methods of the class.


#### `Validators.min()`

Requires that the control's value be **greater than _or equal to_** the specified number:
```
static min: (min: number) => ValidatorFn;
```

=`min="..."` in TDF.


#### `Validators.max()`

Requires that the control's value be **less than _or equal to_** the specified number:
```
static max: (max: number) => ValidatorFn;
```

=`max="..."` in TDF.


#### `Validators.required`

Requires that the control's value be non-empty:
```
static required: ValidatorFn;
```

=`required` attribute in TDF.


#### `Validators.requiredTrue`

Requires that the control's value be true:
```
static requiredTrue: ValidatorFn;
```

**Use case**: in checkboxes.


#### `Validators.email`

Tests the control's value agains a RegEx based on WHATWG spec:
```
static email: ValidatorFn;
```

=`email` attribute in TDF.


#### `Validators.pattern()`

Tests the control's value against a pattern:
```
static pattern: (pattern: string | RegExp) => ValidatorFn;
```

**NB!**: Passing `g` or `y` flags to RegExp is not recommended &mdash; validation can be impure, i.e. produce different results on the same input when run consecutively.

=`pattern="..."` in TDF.


#### `Validators.minLength()`

Requires that the control's **`length` property** be greater than or equal to the specified number:
```
static minLength: (minLength: number) => ValidatorFn
```

**Use case**: for validating either _string_ or **_array_** length.

**NB!**: validator is **NOT invoked if length is zero** &mdash; use `required` additionally.

=`minlength="..."` in TDF (_mind the **lowercase l**_).


#### `Validators.maxLength()`

Requires that the control's **`length` property** be less than or equal to the specified number:
```
static maxLength: (maxLength: number) => ValidatorFn
```

**NB!** Behavior is the same as in `minLength()`.

=`maxlength="..."` in TDF (_mind the **lowercase l**_).


#### `Validators.compose()`

Composes the array of validator fns into a single validator fn which returns a **merged errors map** or null if there are no errors.

**Aside**: `Validators.compose()` is called by NG internally to flatten up all validators passed during the construction of a `FormControl` and assign the flattened validator fn to `FormControl.validator` property.


### Async validators

Respective async counterpars are:
- `AsyncValidator`
- `AsyncValidatorFn`

The only difference is that `AsyncValidatorFn` wraps the result in a `Promise` or `Observable`.

Some rules to follow:
- if an observable is returned it **must complete**
- async validators run _after_ all sync validators
- async validators are NOT run if **any of sync validators failed**
- after async validation begins, form enters a `PENDING` state (available as `pending` prop on a form control)

A common UI pattern to show a loading spinner while async validation is being performed
```
<input ... #name="ngModel" /> // <--- TDF
<input ... formControlName="name" /> // <--- RF

<app-spinner *ngIf="name.pending">
```

**Tip**: By default, all validators run after every form value change, which might be sub-optimal for async validators.  
To optimize this, you can change the `updateOn` property from `change` to `blur` or even `submit`:
```
// TDF (in the template)
<input
  [(ngModel)]="name"
  [ngModelOptions]="{ updateOn: 'blur' }"
/>

// RF (in the component class)
public name = new FormControl('', { updateOn: 'blur' });
```
