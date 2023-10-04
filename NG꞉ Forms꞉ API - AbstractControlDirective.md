---
tags: [Angular/forms]
title: 'NG: Forms: API - AbstractControlDirective'
created: '2023-09-20T13:10:49.862Z'
modified: '2023-10-04T13:37:35.586Z'
---

# NG: Forms: API - AbstractControlDirective

## [`AbstractControlDirective`](https://angular.io/api/forms/AbstractControlDirective) APIs

The root abstract class for all control directives.


### `control`

Holds reference to `AbstractControl` (=model layer) bound to this `AbstractControlDirective`.

+Duplicates many properties of the bound `AbstractControl`.


## Single-control directives family

APIs specific to `FormControl`-based directives (=leaves).


### [`NgControl`](https://angular.io/api/forms/NgControl)-specific APIs


#### `name`

Name of the control.


#### `valueAccessor`

Holds reference to `ControlValueAccessor` (=view layer).


#### (Internal) `FormControl` setup

On initialization, the directive runs `setUpControl()` fn, which roughly does the following:
1. Accepts a `control` (`FormControl`) and a `dir` (`NgControl`)
2. Flattens validators and assigns the resulting validator to `control.validator`
3. Calls `dir.valueAccessor.writeValue()` with `control.value`
4. `setupViewChangePipeline(control, dir)`
5. `setupModelChangePipeline(control, dir)`
6. `setupBlurPipeline(control, dir)`


##### `setupViewChangePipeline(control, dir)` fn

Pipeline that handles **View-to-Model** (`ControlValueAccessor` to `FormControl`) changes.

**Registers a callback** fn via `dir.valueAccessor.registerOnChange()`, which does the following:
1. Caches new value into a set of `_pending` private fields (`_pendingValue/Change/Dirty`)
2. If `updateOn` was set to **`change`**, calls `updateControl(control, dir)`, which:
  2.1. calls `control.markAsDirty()`
  2.2. calls `control.setValue()` (_View to FormControl_) with the pending value and `emitModelToViewChange` set to **false** (this prevents `dir.valueAccessor` to update again)
  2.3. calls `dir.viewToModelUpdate()` (_View to NgModel_) with the pending value (this updates the class field bound to `NgModel`)


##### `setupModelChangePipeline(control, dir)` fn

Pipeline that handles **Model-to-View** (`FormControl` to `ControlValueAccessor`) changes.

**Registers a callback** fn via `control.registerOnChange()`, which does the following:
1. Calls `dir.valueAccessor.writeValue()` with new value (_FormControl to View_)
2. If `emitViewToModelChange` was set to `true`, calls `dir.viewToModelUpdate()` (_FormControl to NgModel_) with the new value (this updates the class field bound to `NgModel`)


##### `setubBlurPipeline(control, dir)` fn

**Registers a callback** fn via `dir.valueAccessor.registerOnTouched()`, which does the following:
1. If `updateOn` was set to **`blur`**, calls `updateControl(control, dir)` only  (see `setupViewChangePipeline()`)
2. Unless `updateOn` was set to **`submit`**, calls `control.markAsTouched()`


### [`FormControlDirective`](https://angular.io/api/forms/FormControlDirective) / [`NgModel`](https://angular.io/api/forms/NgModel)-specific APIs


#### `path`

Path to this control from the top-level container.


#### `isDisabled` (a.k.a. `disabled`)

An `@Input` aliased as `disabled` attribute.

In essence, a shortcut to the bound `FormControl`'s `enable()`/`disable()` setters.


#### `FormControlDirective`-specific APIs


#### `form`(a.k.a. `formControl`)

`form` is an `@Input` on RF `FormControlDirective` (_naming is misleading!, it'd better be `control`_), aliased as `formControl`.

The same is returned by the `control` prop inherited from `AbstractControlDirective`.


#### `NgModel`-specific APIs


##### `name`

`@Input` that is used to register the control with the control container.


##### `model` (a.k.a. `ngModel`) & `update` (a.k.a. `ngModelChange`)

`model`: `@Input` aliased as `ngModel` that binds the Model value, i.e. a field of the component class.

`update`: `@Output` aliased as `ngModelChange` that emits when the View value changes.

Usually used together in a 2-way binding form: `[(ngModel)]="..."`.


##### `options` (a.k.a. `ngModelOptions`)

Optional config options:
- `name` (instead of setting `name` input)
- `standalone`: if `true`, `FormControl` will not be registered with the parent control container
- `updateOn`: when to `updateValueAndValidity()` of the `FormControl`? (`'change'` | `'blur'` | `'submit'`)


##### `formDirective`

Returns the **top-level** `Form` (i.e. RF `FormGroupDirective` or TDF `NgForm`).

**Aside**: the same prop is on all control container directives.


### [`FormControlName`](https://angular.io/api/forms/FormControlName)-specific APIs

#### `name` (a.k.a. `formControlName`)

The name of the nested `FormControl` bound to this directive.

`name` is an `@Input` aliased as `formControlName`.

**NB!** The `name` must correspond to a key in the parent control container.

#### `formDirective`

Returns the **top-level** `Form` (i.e. RF `FormGroupDirective` or TDF `NgForm`).

**Aside**: the same prop is on all control container directives.


## Control container directives family

APIs specific to `FormControl` containers: `FormGroup`, `FormRecord`, `FormArray`.


### [`ControlContainer`](https://angular.io/api/forms/ControlContainer)-specific APIs


#### `name`

Name of the control container.


#### `path`

Path to this control container.


#### `formDirective`

Returns the **top-level** `Form` (i.e. RF `FormGroupDirective` or TDF `NgForm`).


### [`FormGroupDirective`](https://angular.io/api/forms/FormGroupDirective) / [`NgForm`](https://angular.io/api/forms/NgForm)-specific APIs


#### `form`

Returns the `FormGroup` bound to this directive.

In `FormGroupDirective`, it's an `@Input` aliased as `[formGroup]`.

**NB!** Don't confuse `form` (returns bound `FormGroup`) with `formDirective` (returns this `FormGroupDirective` / `NgForm`)

**Aside**: the inherited `control` getter returns the same bound `FormGroup` as `form`.


#### `directives` & `controls` getters

`directives` is on RF `FormGroupDirective`: returns the array of child `FormControlName` directives.

`controls` is on TDF `NgForm`: returns the array of child `AbstractControl`s.


#### `(ngSubmit)` and `submitted`

`submitted`:
  - becomes `true` after native form `submit` event
  - is reset to `false` after form is `reset`

`(ngSubmit)` is an event that emits after native form `submit` event.


### [`FormGroupName`](https://angular.io/api/forms/FormGroupName) / [`NgModelGroup`](https://angular.io/api/forms/NgModelGroup) and [`FormArrayName`](https://angular.io/api/forms/FormArrayName) -specific APIs


#### `name` (a.k.a. `formGroupName` / `formArrayName` / `ngModelName`)

The name of the nested control container bound to this directive.

`name` is an `@Input` aliased by **the same name as the directive** (e.g. `formGroupName` for `FormGroupName` directive, etc.).

**NB!** The `name` must correspond to a key in the parent control container.

