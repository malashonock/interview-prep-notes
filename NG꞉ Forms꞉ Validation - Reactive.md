---
tags: [Angular/forms/reactive]
title: 'NG: Forms: Validation - Reactive'
created: '2023-09-20T08:30:12.408Z'
modified: '2023-10-04T13:38:00.313Z'
---

# NG: Forms: Validation - Reactive

In reactive forms, validation is implemented by passing **validator functions** to the form control model in the component class.


## Setup

1. In the component class, pass an array of validator fns as the 2nd arg:
```
public control = new FormControl(
  '', 
  [Validators.required, Validators.minLength(3)]
);
```

2. In the template, hook into form control error state:
```
<div *ngIf="control.errors?.[required]">
  Value is required
</div>
```


## Custom validators

1. Implement a custom `ValidatorFn`
2. Pass it within the array of validator fns when setting up the form control.


## Cross-field validators

1. Implement a custom `ValidatorFn`
2. Pass it to a common ancestor `FormGroup` as part of its options:
```
public form = this.fb.group({
  password: [''],
  passwordConfirm: [''],
}, {
  validators: PasswordValidators.match('password', 'passwordConfirm'), // <---
});
```


## Async validators

Pass async validators:
- either as part of options object
- or as the 3rd arg, after initial value and sync validators:
```
public control = new FormControl('', {
  asyncValidators: [BackendValidators.checkSth],
  updateOn: 'blur'
});
```

**NB!**: it's recommended to set `updateOn` for performance optimization, to preven async validator from running on each value change.
