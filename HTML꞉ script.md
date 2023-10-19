---
tags: [HTML/script]
title: 'HTML: script'
created: '2023-10-19T12:58:30.874Z'
modified: '2023-10-19T13:36:05.230Z'
---

# HTML: script

[V8 docs](https://v8.dev/features/modules#defer)
[javascript.info](https://javascript.info/script-async-defer)

Default behavior: as the browser comes across a classic `<script>`, it:
- stops building the DOM
- downloads the script (from `src`) as needed
- executes the script
- processes the rest of the page

Important consequences:
- classic `<script>`s don't see DOM elements below them and thus do anything with them (e.g. add event listeners)
- classic `<script>`s block page rendering during their:
  - downloading
  - execution

Historical workaround was to **put plain `<script>`s at the bottom of `<body>`**. Drawbacks:
  - browser starts downloading the script only after processing almost entire HTML &mdash; for long documents, it's a substantial delay


## `<script defer>`

**NB!** `defer` attribute is for external (`src`) scripts only &mdash; failing `src`, `defer` is ignored.

`defer` attribute features:
- the script is **loaded in the background** (=`async`)
- the script is executed **after DOM is ready** (but before `DOMContentLoaded` event)
- it _never_ blocks the page
- order of _loading_ is undefined: the browser decides on its own
- order of _execution_ is **preserved among `defer` scripts**


## `<script async>`

**NB!** `async` attribute is for external (`src`) scripts only &mdash; failing `src`, `async` is ignored.

`async` attribute features:
- the script is **downloaded in the background** (=`defer`)
- the script is executed immediately **after it's downloaded** (regardless of `DOMContentLoaded`)
- it doesn't block the page while loading, but it does **block the page while executing** (if DOM hasn't been build yet)
- order of _loading_ is undefined: the browser decides on its own
- order of _execution_ among `async` scripts: whichever script loads first, runs first


## Dynamic scripts

It's possible to create `<script>` elements at runtime:
```
let script = document.createElement('script');
script.src = '/path/to/script/file';
document.body.append(script); // That's where script loading starts
```

By default, dynamic scripts behave as **`async`**:
- start loading immediately after they get appended to DOM
- execute immediately after they have loaded

**NB!** by setting `script.async = false` explicitly, the script's behavior changes similar to **`defer`**, i.e. such scripts execute in the order they are appended to DOM.


## `<script type="module">`

With regard to the order of loading and execution, ES6 module scripts behave as classic scripts with **`defer`** attribute.

**Note**: by setting `async` on a ESM script, it behaves as `async` script.




