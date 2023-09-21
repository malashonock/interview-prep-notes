---
tags: [Angular/forms]
title: 'Forms: API - ControlValueAccessor'
created: '2023-09-20T12:54:46.397Z'
modified: '2023-09-21T09:07:09.919Z'
---

# Forms: API - [`ControlValueAccessor`](https://angular.io/api/forms/ControlValueAccessor)


## `writeValue()`

Writes a new value to an element.

The new value comes from the **model** (code) and propagates to the view:
1. `FormControl.setValue()`
2. `ControlValueAccessor.writeValue()`
3. Element is updated
4. Change becomes visible in the UI


## `registerOnChange()`

Registers a callback fn that will be called whenever the value changes in the **view** (UI) and will propagate to the model.


## `registerOnTouched()`

Registers a callback fn that will be called whenever a `blur` event occurs and thus notify the **model** (`FormControl`).


## `setDisabledState()` (optional)

Will enable/disable the element according to the specified value.  

Usually is called from the **model**.

