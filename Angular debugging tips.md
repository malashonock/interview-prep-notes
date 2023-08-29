---
title: Angular debugging tips
created: '2023-08-29T14:54:26.029Z'
modified: '2023-08-29T15:40:08.275Z'
---

# Angular debugging tips

#### Get NG component from DOM element

Available on the global `ng` object:

```
const el = document.querySelector('app-some-component');
const cmp = ng.getComponent(el);
cmp.doSomething();
```

**NB!** Element must be any _host_ component (i.e. matching some NG components selector)
