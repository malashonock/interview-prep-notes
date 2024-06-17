---
tags: [Angular/outputs, Angular/templates, Angular/v17+]
title: 'NG: Templates: Function-based outputs'
created: '2024-06-17T09:56:15.958Z'
modified: '2024-06-17T10:11:56.077Z'
---

# NG: Templates: Function-based outputs

[Angular.dev](https://angular.dev/guide/components/output-fn)


## Using `output()` fn

To create an output in a component or directive:
```
public nameChange: OutputEmitterRef<string> = output<string>();

public onNameChange(newName: string): void {
  this.nameChange.emit(newName);
}
```

Outputs created this way are bindable via event binding:
```
<my-comp (nameChange)="setNewName($event)" />
```

Outputs can be subscribed to programmatically, if need be (e.g. if a component is created dynamically):
```
const myComp = this.viewContainerRef.createComponent(...);
myComp.instance.nameChange.subscribe(...);
```


## Options

To **alias** the output:
```
public nameChange = output<string>({ alias: 'ngxNameChange' });
```


## Interop with RxJS observables

- `outputFromObservable()`
- `outputToObservable()`


## Function- vs. decorator-based outputs

- Simpler API, than that of RxJS &mdash; just call `.emit()` on the output
- Better type safety

