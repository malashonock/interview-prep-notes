---
tags: [Angular/bindings]
title: 'NG: Event binding'
created: '2023-09-12T14:16:56.882Z'
modified: '2023-10-04T13:37:26.793Z'
---

# NG: Event binding

**Tip** When setting inputs' event handlers in a template, use `getValue($event)` instead of `$event.target` for type-safety:
```
<input (change)="onChange(getValue($event))" />
```
