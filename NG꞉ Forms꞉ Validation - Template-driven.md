---
tags: [Angular/forms/template-driven]
title: 'NG: Forms: Validation - Template-driven'
created: '2023-09-19T15:17:29.594Z'
modified: '2023-10-04T13:38:02.867Z'
---

# NG: Forms: Validation - Template-driven

In template-driven forms, validation is implemented by applying validation **attributes** and **directives** in the template.


## Setup

1. Apply validator attributes/directives to validated inputs:
```
<input
  ...
  required // <--- triggers built-in Validators.required validator
  minlength="4" // <--- triggers built-in Validators.minLength() validator
/>
```

2. Export input's `NgModel` to a template variable:
```
<input
  ...
  #name="ngModel"
/>
```

3. Use `NgModel` to set up error messages:
```
<div *ngIf="name.invalid && (name.dirty || name.touched)">

  <div *ngIf="name.errors?.['required']">
    Name is required.
  </div>
  
  <div *ngIf="name.errors?.['minlength']">
    Name must be at least 4 characters long.
  </div>

  <div *ngIf="name.errors?.['forbiddenName']">
    Name cannot be Bob.
  </div>

</div>
```


## Custom validators

1. Create a validator directive class:
- the directive must implement `Validator` interface (`validate` and optionally `registerOnValidatorChange`)
- if the directive accepts 1 single `@Input`, it's common to alias its name with the directive's selector
- register the directive in the `NG_VALIDATORS` registry by providing it in `@Directive.providers` array:
```
{
  provide: NG_VALIDATORS,
  useExisting: MyValidatorDirective,
  multi: true,
}
```

**NB!** `useExisting` must be used, because the registered validator must be _a specific instance_ of the directive, with its `@Input` bound to a specific value.

2. Apply custom directive in the template:
```
<input
  ...
  appForbiddenName="bob" // <--- custom validator directive
/>

...

<div *ngIf="name.errors?.['forbiddenName']">
    Name cannot be Bob.
  </div>
```


## Cross-field validators

Same general rules apply as for other custom validators.

Differences from field-level validators:

1. Validator directive must be applied to the entire `<form>` rather than on an `<input>`:
```
<form
  ...
  #loginForm="ngForm"
  appPasswordsMatch
>
  ...
</form>
```

2. Error state must be read from the top-level `FormGroup`:
```
<div
  *ngIf="
      loginForm.errors?.['passwordsMismatch']
      && (loginForm.touched || loginForm.dirty)
    "
>
  Passwords must match
</div>
```


## Async validators

Must be registered under `NG_ASYNC_VALIDATORS` token.

