---
tags: [Angular/forms]
title: Forms
created: '2023-09-12T14:16:56.882Z'
modified: '2023-09-12T14:20:29.813Z'
---

# Forms

**Tip** When setting inputs' event handlers in a template, use `getValue($event)` instead of `$event.target` for type-safety:
```
<input (change)="onChange(getValue($event))" />
```
