---
tags: [Angular/inputs, Angular/signals]
title: 'NG: Signals: Model inputs'
created: '2024-06-10T15:34:17.087Z'
modified: '2024-06-10T15:53:37.241Z'
---

# NG: Signals: Model inputs

Model inputs are like plain signal inputs, but they also allow to **set input values inside the component**:
```
checked = model(false);

// ...later in the component class
this.checked.set(!this.checked());
```

**NB!** `model(...)` creates not only an input, but also an output suffixed with `...Change` to enable 2-way binding.

When a component sets the value of a model input, this value is also propagated up to the component that is binding a value to this input:
```
// in a parent component's template
// notice, in case isAdmin is a signal, the entire signal is passed, not its value!
// banana-in-the-box is possible, because NG automatically creates checkedChange output for us
<my-checkbox [(checked)]="isAdmin" />

// in a parent component's class
// bound value can be a signal
isAdmin = signal(false);
// ...or a plain value
isAdmin = false;
```

**Use cases**:
Use model inputs in components that modify a value based on user interaction.


## Customizing model inputs

- use `model.required(...)` to make a model input required
- aliases are supported
- value transforms are NOT supported





