---
title: Angular debugging tips
created: '2023-08-29T14:54:26.029Z'
modified: '2023-08-29T15:02:22.230Z'
---

# Angular debugging tips

#### Get NG component

Available on the global `ng` object:

```
const rootEl = document.querySelector('app-root');
const rootCmp = ng.getComponent(rootEl);
rootCmp.doSomething();
```
