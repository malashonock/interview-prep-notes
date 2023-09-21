---
tags: [Angular/forms]
title: 'Forms: Class hierarchy'
created: '2023-09-19T14:50:15.741Z'
modified: '2023-09-21T09:03:02.079Z'
---

# Forms: Class hierarchy


## Overview

[A thorough explanation of NG forms](https://indepth.dev/posts/1143/a-thorough-exploration-of-angular-forms)

`AbstractControl` is **model** layer: it stores the data related to a specific entity.  

`ControlValueAccessor` is **view** layer: connects a DOM element or custom NG component with model layer (`AbstractControl`) through `AbstractControlDirective`.

`AbstractControlDirective` is an **intermediary** between the view (`ControlValueAccessor`) and the model (`AbstractControl`).

**Aside**: multiple `AbstractControlDirective`s can bind multiple `ControlValueAccessor`s to **one** `AbstractControl`.

_View-to-Model_ = user types into an input.  
_Model-to-View_ = program code updates input value.  


## [Control classes hierarchy](https://angular.io/api/forms/AbstractControl)  

- `AbstractControl`
  - `FormControl`
  - `FormGroup`
    - `FormRecord`
  - `FormArray`

`FormControl` is a "leaf".  

Only `FormControl`s can be leaves.  

Only `FormControl`s can directly interact with a `ControlValueAccessor`.  

`FormControl` can be standalone, i.e. not belong to any container.

All other are containers that can't be leaves.


## [Directive classes hierarchy](https://angular.io/api/forms/AbstractControlDirective)

- `AbstractControlDirective`
  - `ControlContainer` (_abstract_)
    - `AbstractFormGroupDirective`
      - `NgModelGroup`: TDF, `ngModelGroup` (NOT top-level)
      - `FormGroupName`: RF, `formGroupName` (NOT top-level)
    - `NgForm` (_implements `Form` interface_): TDF, implicitly applied to `<form>` (top-level)
    - `FormGroupDirective` (_implements `Form` interface_): RF, top-level `[formGroup]` binding
    - `FormArrayName`: RF, `formArrayName`
  - `NgControl` (_abstract_)
    - `NgModel`: TDF, `[(ngModel)]`
    - `FormControlName`: RF, `formControlName`
    - `FormControlDirective`: RF, standalone `[formControl]` binding

**Rule of thumb**:
- `FormGroupDirective` / `NgForm` can only be the top-level container
- `FormGroupName` / `NgModelName` / `FormArrayName` can only be nested containers, using them at top-level will result in an error.

**Aside**: `formDirective` prop is available on:
- all `ControlContainer` directives
- some `NgControl` directives, namely:
  - RF `FormControlName` directive
  - TDF `NgModel` directive


## [ControlValueAccessor hierarchy](https://angular.io/api/forms/ControlValueAccessor)

- `ControlValueAccessor` (interface)
  - `DefaultValueAccessor`: default for all `<input>`s except `[type=checkbox]` and `<textareas>`s
  - `NumberValueAccessor`: for `input[type=number]`
  - `CheckboxControlValueAccessor`: for `input[type=checkbox]`
  - `RadioControlValueAccessor`: for `input[type=radio]`
  - `RangeValueAccessor`: for `input[type=range]`
  - `SelectControlValueAccessor`: for `<select>`s without `multiple` attribute
  - `SelectMultipleControlValueAccessor`: for `<select>`s with `multiple` attribute
