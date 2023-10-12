---
tags: [Angular/directives]
title: 'NG: Directives: Built-in attribute directives'
created: '2023-10-10T14:26:56.705Z'
modified: '2023-10-10T14:35:45.200Z'
---

# NG: Directives: Built-in attribute directives


## `NgClass`

Adds or removes a _set_ of classes, i.e. a record where:
- keys are CSS class names
- values resolve to truthy/falsy values indicating whether or not to apply the respective CSS class

**Note**: `NgClass` dir is almost identical to `[class]` binding &mdash; class binding is recommended.


## `NgStyle`

Adds or removes a _set_ of styles, i.e. a record where:
- keys are CSS style names
- values resolve to string CSS style values

**Note**: `NgStyle` dir is almost identical to `[style]` binding &mdash; style binding is recommended.


## `NgModel`

See notes on template-driven forms.
