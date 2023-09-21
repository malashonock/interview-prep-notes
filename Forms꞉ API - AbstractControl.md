---
tags: [Angular/forms]
title: 'Forms: API - AbstractControl'
created: '2023-09-20T09:28:40.715Z'
modified: '2023-09-21T07:33:18.499Z'
---

# Forms: API - AbstractControl

## [`AbstractControl`](https://angular.io/api/forms/AbstractControl) APIs


### Managing value(s)


#### `value` getter

The _current_ (=snapshot) value of the `FormControl`.

For `FormGroup` and `FormArray`, the values of currently **enabled** controls.

**NB!**: For a _disabled_ `FormGroup`, `value` still returns the value of all its form controls (not a `{}`).


#### `getRawValue()`

Returns the current value of the `FormControl`.

For `FormGroup` and `FormArray`, returns the values of **all** controls (regardles of their `disabled` status).


#### `valueChanges` getter

An **observable** stream of value snapshots.

The observable emits every time:
- control value(s) change(s) **in the UI**
- control value(s) change(s) **programmatically**
- **`enable()`** or **`disable()`** is called without passing `{ emitEvent: false }`

**NB!**: the emit happens on _this_ control right after its value changed, but **before the value of the parent control is updated** (e.g. of the parent `FormGroup`) &mdash; if parent's value is considered, check to `valueChanges` of the parent.


#### `value` setters

Setters (_abstract_):
- `setValue()`: overwrites entire `value`
- `patchValue()`: partially updates `value`
- `reset()`: resets `value`, i.e.:
  - marks it as `pristine`
  - marks it as `untouched`
  - resets its value to:
    - `null`, by default
    - initial value, if `nonNullable` was specified
  - for control containers, does the above for all sub-controls

Setter options vary by concrete class:
- in all subclasses, all 3 setters have options:
  - `onlySelf`
  - `emitEvent`, affects:
    - `valueChanges`
    - `statusChanges`
- **Additionally**,`FormControl`'s `setValue()` and `patchValue()` have 2 more options:
  - **`emitModelToViewChange`**: if `true`, triggers all **onChange callbacks** registered on this `FormControl` during `setupModelChangePipeline()`
    - **`emitViewToModelChange`**: if `true` and `FormControl`'s onChange callbacks are triggered by `emitModelToViewChange`, `ngModelChange` event will be fired on **`NgModel`**


### Managing errors

Getters:
- `errors`: returns all `ValidationErrors` on this control, or `null`
- `getError()`: similar to `error`, but allows find an error with a particular `errorCode` on this control or any sub-control specified in `path` (array of strings or dot-delimited string)
- `hasError()`: similar to `getError()`, but returns a boolean indicating if a specified error was found on the specified (sub-)control

Setters:
- `setErrors()`, options:
  - `emitEvent`, affects:
    - `statusChanges`

**NB!**: Calling `setErrors()` updates validity of the parent control, too.


### Managing status


#### `status` getter

The _current_ status of the control:
```
type FormControlStatus =
  | 'VALID'
  | 'INVALID'
  | 'PENDING'
  | 'DISABLED';
```
Description:
- `'VALID'`: no errors
- `'INVALID'`: some errors are present
- `'PENDING'`: async validators are running
- `'DISABLED'`: control is disabled and excluded from parent's value & validity calculations

**NB!**: Statuses are **mutually exclusive**.


#### `statusChanges` getter

An observable stream of `status` snapshots.


#### `valid` / `invalid`

Getters:
- `valid`: `status` is `'VALID'`.  
- `invalid`: `status` is `'INVALID'`.  


#### `pristine` / `dirty`

Getters:
- `pristine`: control value has NEVER changed since initialization
- `dirty`: control's value HAS changed since initialization (at least once)

Setters:
- `markAsDirty()`
- `markAsPristine()`

All setters accept the following `options`:
- `onlySelf`

#### `touched` / `untouched`

Getters:
- `touched`: `focus` -> `blur` events have been fired on the control
- `untouched`: opposite of `touched`

Setters:
- `markAsTouched()`, options: 
  - `onlySelf`
- `markAllAsTouched()` (_no options_)
- `markAsUntouched()`, options: 
  - `onlySelf`

#### `enabled` / `disabled`

Getters:
- `disabled`: `status` is `'DISABLED'`.  
- `enabled`: opposite of `disabled`.

Setters:
- `disable()`
- `enable()`

All setters accept the following `options`:
- `onlySelf`
- `emitEvent`, triggers:
  - `statusChanges`
  - `valueChanges`

#### `pending`

Getters:
- `pending`: `status` is `'PENDING'`

Setters:
- `markAsPending()`, options: 
  - `onlySelf`
  - `emitEvent`, triggers:
    - `statusChanges`


### Parent-child navigation

Getters:
- `parent`: the immediate parent control (`null` if none)
- `root` getter: the ultimate top-level ancestor of the control
- `get()`: retrieve a child control given its name or path (or `null` if not found)

Setters:
- `setParent()`


### Common setter options

On some `markAsXXX` and `setXXX` methods, the following options are available:
- `onlySelf`:
  - `true`: changes will apply only to this control
  - `false` (=default): changes will propagate up to all ancestors of this control
- `emitEvent`:
  - `true` (=default): the related observable (`statusChanges` and/or `valueChanges`) will emit an event with the change applied


## [`FormControl`](https://angular.io/api/forms/FormControl)-specific APIs


### `defaultValue`

The value to which the control is `reset()`:
- `null` by default 
- initial value passed to the Ctor, if `nonNullable` is set to `true`


### `registerOnChange()`

Registers an onChange callback that will be fired during Model-to-View update.

Called internally in `setupModelChangePipeline()`.


### `registerOnDisabledChange()`

Registers a callback that will be fired any time `disabled` state changes.

Called internally in `setupDisabledChangeHandler`: there it registers the `ControlValueAccessor.setDisabledState` handler with the `FormControl`.  
Thus, whenever the Model (`FormControl`) changes its `disabled` state, the View (`ControlValueAccessor`) takes appropriate action to update itself. 


## [`FormGroup`](https://angular.io/api/forms/FormGroup)-specific APIs

Adds members for managing sub-controls (as dictionary).

Getters:
- `controls`: returns an **object** where keys = sub-control names, and values = sub-controls
- `contains()`: is there an **enabled** control with the given name in this `FormGroup`?

Setters:
- `registerControl()`: adds control to `FormGroup`, but **doesn't** update `FormGroup`'s value and validity
- `addControl()`: calls `registerControl()`, then updates `FormGroup`'s value and validity
- `setControl()`: replaces an existing control with the given name
- `removeControl()`: removes an existing control with the given name

**NB!**: If an already existing sub-control name is passed to `registerControl()`, it doesn't add anything and just returns the existing sub-control.


## [`FormArray`](https://angular.io/api/forms/FormArray)-specific APIs

Adds members for managing sub-controls (as array).

Getters:
- `controls`: returns an **array** of sub-controls
- `length`: length of the array of sub-controls
- `at()`: returns a sub-control at the given index (= `Array#at()`)

Setters:
- `push()`: appends the given sub-control to the end of the array
- `insert()`: inserts the given sub-control at the given index
- `setControl()`: replaces an existing control at the given index 
- `removeAt()`: removes a sub-control at the given index
- `clear()`: removes all sub-controls from the `controls` array

**NB!**: Wherever a method accepts an `index` as param, _negative_ index is treated as _count back from the end_, i.e. `index` + `length`.

**NB!**: If (adjusted) `index` is out of bounds, behaviour is based on `Array#splice()`:
- `insert()`:
  - `index` too large: will append at the end
  - `index` too small: will prepend to the start
- `removeAt()`:
  - `index` too large: nothing will be deleted
  - `index` too small: 1st element will be deleted
- `setControl()`:
  - `index` too large: will append as new control at the end
  - `index` too small: 1st element will be replaced

All above setters have the same `options` param:
  - `emitEvent`, affects:
    - `valueChanges`
    - `statusChanges`



