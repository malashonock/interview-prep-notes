---
tags: [Angular/components, Angular/v16+]
title: 'NG: Components: Required inputs'
created: '2024-01-05T15:03:28.155Z'
modified: '2024-01-05T15:06:25.353Z'
---

# NG: Components: Required inputs

Since NG v16, an `@Input` can be made required as follows:
```
@Input({ required: true }) inputName!: InputType;
```

With this setting, omission of such input in a template that uses this component will generate a compile-time error.
