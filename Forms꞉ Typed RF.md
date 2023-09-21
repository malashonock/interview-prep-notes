---
tags: [Angular/forms/reactive]
title: 'Forms: Typed RF'
created: '2023-09-20T08:49:12.641Z'
modified: '2023-09-20T09:26:59.960Z'
---

# Forms: Typed RF


## Implicit inference

Starting from Angular 14, NG can infer the type of form control values implicitly.


## Explicit typing


### Typing `FormControl`

If using bare `FormControl` API, specify type in the Ctor call:
```
public control = new FormControl<string | null>(null);
```

When using `FormBuilder`:
```
public form = this.fb.group({
  name: [null as string | null],
})
```


### Typing `FormGroup`

It's more convenient to declare a separate interface:
```
interface LoginForm {
  name: FormControl<string>; // required, non-nullable
  password?: FormControl<string>; // optional, non-nullable
}

public form = new FormGroup<LoginForm>({
  name: ['', { nonNullable: true }],
  password: ['', { nonNullable: true }],
});
```

**Tip**: Use a custom TS utility type to derive form values interface from an DTO/entity interface:
```
type Form<TFields> = {
  [TFieldName in keyof TFields]: TFields[TFieldName] extends object
    ? TFields[TFieldName] extends any[]
      ? FormArray<TFields[TFieldName]>
      : FormGroup<TFields[TFieldName]>
    : FormControl<TFields[TFieldName]>
}
```


### Typing `FormArray` and `FormRecord`

```
public indexedControls = new FormArray<FormControl<string>>([]);
public namedControls = new FormRecord<FormControl<string>>([]);
```

## Nullability

By default, NG infers the type of an implicitly typed control as a union with `null` &mdash; this is because calling `reset()`, by default, will set the control's value to `null`:
```
public control = new FormControl('');
// typeof control.value === string | null
```

To make a control non-nullable (not to be confused with `''`!), set `nonNullable` in the Ctor options:
```
public control = new FormControl('', { nonNullable: true });
// typeof control.value === string
```

If `reset()` is called on a non-nullable control, its value is reset to the initial value in the Ctor.

**Tip**: If all controls in a form must be non-nullable, use `fb.nonNullable` prop:
```
public form = this.fb.nonNullable.group({
  name: '', // string
  password: '', // string
})
```


## `value` vs. `getRawValue()` type

Type of form group's `value` is `Partial<{ ... }>`, because 
some form controls can be disabled, and `value` excludes values of disabled controls.

However, type of form group's `getRawValue()` is simply `{ ... }`, because `getRawValue()` always returns all values, including those of disabled controls.

