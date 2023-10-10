---
tags: [Angular/templates]
title: 'NG: Templates: Event binding'
created: '2023-09-12T14:16:56.882Z'
modified: '2023-10-10T13:43:28.356Z'
---

# NG: Templates: Event binding

[Event binding @NG docs](https://angular.io/guide/event-binding)
[User input @NG docs](https://angular.io/guide/user-input)

## Tips and tricks

**Tip**: When setting inputs' event handlers in a template, use **`getValue($event)`** instead of `($event.target as HTMLInputElement).value` for type-safety:
```
<input (change)="onChange(getValue($event))" />
```

**Tip**: Leverage **template reference variables** instead of `$event`:
```
<input #name (keyup)="void(0)" />
<p>name.value</p>
``` 

**Tip**: Use key event filtering, e.g. **`(keyup.enter)="..."`** instead of `(keyup)="..."` and using `switch..case` in the event callback.

**Tip**: Modifier keys can also be used in key event filtering, e.g. **`(keyup.shift.alt.t)="..."`**.


