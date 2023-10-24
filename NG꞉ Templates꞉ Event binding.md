---
tags: [Angular/templates]
title: 'NG: Templates: Event binding'
created: '2023-09-12T14:16:56.882Z'
modified: '2023-10-24T09:34:00.691Z'
---

# NG: Templates: Event binding

[Event binding @NG docs](https://angular.io/guide/event-binding)
[User input @NG docs](https://angular.io/guide/user-input)


## Tips and tricks


### Using `$event`

**Tip**: When setting inputs' event handlers in a template, use **`getValue($event)`** instead of `($event.target as HTMLInputElement).value` for type-safety:
```
<input (change)="onChange(getValue($event))" />
```

**Tip**: Leverage **template reference variables** instead of `$event`:
```
<input #name (keyup)="void(0)" />
<p>name.value</p>
``` 


### Keyboard events

**Tip**: Use key event filtering, e.g. **`(keyup.enter)="..."`** instead of `(keyup)="..."` and using `switch..case` in the event callback.

**Tip**: Modifier keys can also be used in key event filtering, e.g. **`(keyup.shift.alt.t)="..."`**.

**Tip**: You can use the `code` field to use the more specific `KeyboardEvent.code` instead of the default `KeyboardEvent.key`, e.g. **`(keyup.code.shiftleft.altleft.keyt)="..."`**.


### Passive events

To make all event listeners [passive](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#using_passive_listeners):
1. Create a file `zone-flags.ts` under `src` directory
2. Specify the array of events, whose listeners should be made passive:
```
(window as any)['__zone_symbol__PASSIVE_EVENTS'] = ['scroll'];
```
3. In the `src/polyfills.ts` file (or in `angular.json`), **before** importing `zone.js`, import the newly created `zone-flags`.


