---
tags: [Angular/inputs, Angular/signals]
title: 'NG: Signals: Signal inputs'
created: '2024-06-10T14:12:23.827Z'
modified: '2024-06-10T15:33:10.161Z'
---

# NG: Signals: Signal inputs

Signal inputs (`InputSignal`) derive from read-only signals (`Signal`) and share the same common features:
- they can be used in `computed`s
- they can be used in `effect`s

**Optional** signal inputs:
```
name = input<string>();
age = input(0);
```

**Required** signal inputs:
```
name = input.required<string>();
```

To use input signal value in the template, call the signal fn:
```
<p>label()</p>
```


## Signal input transforms

```
disabled = input(false, {
  transform: booleanAttribute,
});
```


## Signal input aliases

```
nameInTheComponentClass = input(defaultValue, {
  alias: 'nameInTheTemplate',
});
```


## Monitoring signal input changes

Use `effect` fn (within an injection context).


## Signal vs. decorator-based inputs

Benefits of signal inputs:
- better type safety, no need for TS type assertions for required inputs
- signal inputs automatically mark OnPush components as dirty
- monitoring input changes via `effect` is easier than via `ngOnChanges`

