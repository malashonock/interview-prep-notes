---
tags: [Angular/forms]
title: 'Forms: Overview'
created: '2023-09-19T11:21:02.618Z'
modified: '2023-09-20T11:43:08.811Z'
---

# Forms: Overview

There are 2 approaches to creating forms in NG:
- **Template-driven forms**
- **Reactive forms**


## General comparison

Criterion | Template-driven forms | Reactive forms
--- | --- | ---
Implementation mechanism | Directives applied in template | Observable streams
Access to form's underlying object model | **Indirect, implicit**: rely on directives applied to the template to manipulate form's state | **Direct, explicit**: from inside the component class
Mutability | **Mutable**: due to async nature of 2-way data binding, you can't be sure form state can't be changed while you're working with it | **Immutable**: state is changed via API, each state change returns a new state
Data flow | **Asynchronous**: model-to-view updates are async | **Synchronous**
Form validation | **Harder**: via validator directives | **Easier**: via validator fns
Scalability | **Bad** | **Good**
Testability | **Worse**: need to consider how CD interleaves in form data flow | **Better**: can be tested without rendering the UI and interacting with CD
Separation of concerns | Template becomes overloaded with validation logic | Template is cleaner, most logic is in the component class


## Application comparison

Task | Template-driven forms | Reactive forms
--- | --- | ---
Setup containing `<form>` | no setup, `NgForm` directive is applied automatically | bind top-level `FormGroup` to `[formGroup]` directive
Setup form controls | bind component class field to `[(ngModel)]` directive | assign control name to `formControlName` directive
input `id` attribute | irrelevant, needed only for `<label>`s binding | irrelevant, needed only for `<label>`s binding
input `name` attribute | **must be set explicitly** for the control to be attached to the parent `FormGroup` | not needed, `formControlName` does the job
Handle form submit | bind to `(ngSubmit)` event | bind to `(ngSubmit)` event
Setup nested form control groups | assign form group name to `ngModelGroup` directive | assign form group name to `formGroupName` directive
Setup nested form control arrays | no specific directive | assign form array name to `formArrayName` directive

In **template-driven** forms, it's common to use template variables to manipulate `FormGroup`s and `FormControls` from within the template, e.g.:
- to reference an `NgForm` directive instance (`ngForm` is the value of `NgForm.exportAs`):
```
<form ... #loginForm="ngForm">
  ...
</form>
```
**Use case**: to bind the submit button `[disabled]` property to form validity state.
- to reference an `NgModel` directive instance (`ngModel` is the value of `NgModel.exportAs`):
```
  ...
  <input 
    ...
    [(ngModel)]="vm.userName"
    #userNameControl="ngModel"
  /> 
  ...
```
**Use case**: to reference control state in error message templates.

In **reactive** forms, template variables are rarely used &mdash; as a rule, respective component class fields are referenced.


## Foundation classes

Both approaches share common foundation classes:
- `FormControl`: for an individual control
- `FormGroup`: for a collection of controls with statically known names (=object)
- `FormRecord`: for a dynamic collection of controls manipulated by name, but name is not known statically
- `FormArray`: for a dynamic collection of controls manipulated using index, like an array
- `ControlValueAccessor`: an interface between a form control and the DOM element / NG component

The 2 approaches differ in how they create and manage form control instances.


## Data flow

### View-to-model

The beginning is common to both approaches:
1. User changes input value.
2. Native `<input>` emits `input` event with the latest value.
3. `DefaultValueAccessor` directive, applied implicitly to the `<input>`, triggers the callback registered in `registerOnChange()`.

Subsequently, different things happen depending on the type of a form:

In a **template-driven** form:

4. The onChange callback calls the `viewToModelUpdate()` method of the `NgModel` directive, which emits an `ngModelChange` event.
5. Via 2-way binding, the respective component class field value gets updated.

In a **reactive** form:

4. The same onChange callback also calls the `setValue()` method of the bound `FormControl`.
5. `FormControl` emits a new value through the `valueChanges` observable and thus notifies all subscribed observers.

### Model-to-view

In a **template-driven** form:
1. The respective component class field value gets updated
2. Because the changed value is bound to an input on the `NgModel` directive instance, `ngOnChanges` hook is called on `NgModel` during CD.
3. `ngOnChanges` schedules an async task to `setValue()` on the internal `FormControl`
4. During the _next_ tick, `setValue()` is called on the `FormControl`

In a **reactive** form:
1. Somewhere in the component class, `setValue()` is called _directly_ on the `FormControl`

Subsequently, the same things happen in both approaches:
- `FormControl` emits a new value through the `valueChanges` observable and thus notifies all subscribed observers.
- `DefaultValueAccessor` updates the value of the native `<input>`
- The user sees the updated value on the screen


