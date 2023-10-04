---
tags: [Angular/forms/template-driven]
title: 'NG: Forms: Setup - Template-driven'
created: '2023-09-19T11:19:13.866Z'
modified: '2023-10-04T13:37:50.950Z'
---

# NG: Forms: Setup - Template-driven

Before all, import `FormsModule`.


## Create the containing form

In the template, just add the native `<form>` element.

NG will automatically activate `NgForm` directive for any `<form>` element.

`NgForm` directive creates the top-level `FormGroup` instance.


## Configure form controls

In the component class, define a public instance field that will keep track of control value:
```
public userName = '';
```

In the template, set input name & bind the field to `[(ngModel)]` directive:
```
  ...
  <input
    name="userName" 
    [(ngModel)]="userName"
  />
  ...
```


### Bind submit handler

On the containing `<form>`, bind `(ngSubmit)` event to the submit handler:
```
<form ... (ngSubmit)="onSubmit()">
  ...
</form>
```


