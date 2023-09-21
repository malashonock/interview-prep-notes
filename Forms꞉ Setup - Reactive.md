---
tags: [Angular/forms/reactive]
title: 'Forms: Setup - Reactive'
created: '2023-09-19T11:21:32.996Z'
modified: '2023-09-19T14:46:57.640Z'
---

# Forms: Setup - Reactive

Before all, import `ReactiveFormsModule`.


## (Optionally) Dependency injection

It's recommended to use `FormBuilder` API.

To that end, an instance of `FormBuilder` needs to be injected in component class Ctor:
```
public constructor(private fb: FormBuilder) {}
```


## Configure underlying form model

In component class:
1. Create a public instance field of type `FormGroup`:
```
// Manually:
public form = new FormGroup({
  userName: new FormControl(''),
  password: new FormControl(''),
});

// The same, using FormBuilder:
public form = this.fb.group({
  userName: [''],
  password: [''],
})
```


## Wire up form group to template

1. On the containing `<form>` element, bind `form` from the component class to `[formGroup]` directive:
```
<form [formGroup]="form>
  ...
</form>
```

2. On each of the inputs, assign name string (=name of control in the `FormGroup`) to `formControlName` directive:
```
  ...
  <input ... formControlName="userName" />
  ...
```

**NB!**: form controls can be bound any component that implements `ControlValueAccessor`:
- native `<input>`s are implicitly applied the directive that implements the `DefaultValueAccessor`
- for custom/derivative inputs, component class must implement `ControlValueAccessor` explicitly

3. On the containing `<form>`, bind `(ngSubmit)` event to the submit handler:
```
<form ... (ngSubmit)="onSubmit()">
  ...
</form>
```


## Single form control

In the component class, add a public instance field of type `FormControl`:
```
public userName = new FormControl('');
```

In the template, bind the form control to an input:
```
  ...
  <input ... [formControl]="userName" />
  ...
```


## Nested form group

In the component class, define a nested `FormGroup` as one of the controls in the top-level `FormGroup`:
```
public form = this.fb.group({
  ...
  address: this.fb.group({
    country: [''],
    city: [''],
  }),
});
```

In the template, bind any DOM container element to nested `FormGroup` using `formGroupName` directive:
```
  ...
  <fieldset formGroupName="address"> // <---
    <input ... formControlName="country" />
    <input ... formControlName="city" />
  </fieldset>
  ...
```


## Array of unnamed form controls

In the component class, define a `FormArray` as one of the controls in the top-level `FormGroup`:
```
public form = this.fb.group({
  roles: this.fb.array([
    ['user'],
    ['admin'],
  ]),
})
```

In the template, bind any DOM container element to `FormArray` using `formArrayName` directive, and each input in the array **by index** using `formControlName` directive:
```
  ...
  <div formArrayName="roles"> // <---
    <input 
      ...
      *ngFor="
        let role of form.controls['roles'].controls;
        let i = index
      "
      [formControlName]="i" // <--- name bound to index
      />
  </div>
  ...
```

Usually, dynamic form control collections are manipulated in the component class using their APIs.

