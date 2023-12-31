---
tags: [Angular/debugging]
title: 'NG: Debugging tips'
created: '2023-08-29T14:54:26.029Z'
modified: '2023-10-04T13:36:10.532Z'
---

# NG: Debugging tips

#### Inspector shortcuts

`$0` refers to the DOM element clicked upon on the browser Inspector tab.  
`$ng0` refers to the NG element clicked upon on the NG dev tools tab.

#### Get NG component from DOM element

Available on the global `ng` object:

```
const el = document.querySelector('app-some-component');
const cmp = ng.getComponent(el);
cmp.doSomething();
```

**NB!** Element must be any _host_ component (i.e. matching some NG components selector)
